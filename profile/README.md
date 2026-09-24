## Quick start - More info at [go-tstr.com](https://go-tstr.com)

```go
package app_test

import (
	"testing"

	"github.com/go-tstr/tstr"
	"github.com/go-tstr/tstr/dep/cmd"
	"github.com/go-tstr/tstr/dep/container"
	"github.com/testcontainers/testcontainers-go/modules/postgres"
)

func TestMain(m *testing.M) {
	tstr.RunMain(m, tstr.WithDeps(
		container.New(
			container.WithModule(postgres.Run, "postgres:16-alpine"),
		),
		cmd.New(
			cmd.WithGoCode("../", "./cmd/my-app"),
			cmd.WithReadyHTTP("http://localhost:8080/ready"),
		),
	))
}
```
