# testenv

```go
import (
	"testing"

	"github.com/qor5/admin/v3/internal/testenv"
	"gorm.io/gorm"
)

type TestModel struct {
	gorm.Model
	Description string
}

var db *gorm.DB

func TestMain(m *testing.M) {
	env, err := testenv.New().DBUser("sam").SetUp()
	if err != nil {
		panic(err)
	}
	defer env.TearDown()

	// some initialization
	db = env.DB
	if err = db.AutoMigrate(&TestModel{}); err != nil {
		panic(err)
	}

	m.Run()
}

func TestSelectVersion(t *testing.T) {
	var version string
	if err := db.Raw("SELECT version()").Scan(&version).Error; err != nil {
		t.Fatal(err)
	}
	t.Logf("current database version: %q", version)
}

func TestSetupTestEnv(t *testing.T) {
	// If you don't want to initialize in TestMain
	env, err := testenv.New().SetUpWithT(t)
	if err != nil {
		t.Fatal(err)
	}
	var version string
	if err := env.DB.Raw("SELECT version()").Scan(&version).Error; err != nil {
		t.Fatal(err)
	}
	t.Logf("current database version: %q", version)
}
```

If you encounter this error: `Failed to get image auth for https://index.docker.io/v1/. Setting empty credentials for the image: postgres:16.3-alpine`, try `docker pull postgres:16.3-alpine` first.