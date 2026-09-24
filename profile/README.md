# go-tstr

**Application, integration and black-box tests as plain Go tests.**

A small ecosystem for declaring test dependencies (containers, compose stacks, CLI commands, your own Go binary) and letting a runner start them, wait until they are ready, run the tests, and tear everything down in reverse order.

[**go-tstr.com**](https://go-tstr.com) · [godoc](https://pkg.go.dev/github.com/go-tstr/tstr)

## Repositories

| Repo | What |
|---|---|
| [`tstr`](https://github.com/go-tstr/tstr) | The runner. One small `Dependency` interface (`Start`, `Ready`, `Stop`) plus `RunMain` and `Run` helpers. Ready-made dependencies under `dep/`: `cmd`, `compose`, `container`, `depfn`, `deptest`. |
| [`golden`](https://github.com/go-tstr/golden) | Golden file assertions for strings and HTTP responses. Recreate expected output with an env flag, review the diff in git. |

## Why tstr

- **One small interface.** Every dependency implements `Start`, `Ready`, `Stop`. The runner starts them in order, waits until each is ready, and stops them in reverse.
- **Tests stay tests.** Hook the runner into `TestMain` with `RunMain`, or scope it to a single test with `Run`. Plain `go test`, no extra CLI.
- **Batteries included.** Docker containers via testcontainers-go, compose stacks, arbitrary commands, and `cmd.WithGoCode` to compile and run your own service with coverage instrumentation.
- **Safe teardown.** Stop runs on every started dependency even when a later one fails to start, and errors are aggregated instead of swallowed.

## Quick start

```go
package app_test

import (
	"os"
	"testing"

	"github.com/go-tstr/tstr"
	"github.com/go-tstr/tstr/dep/cmd"
	"github.com/go-tstr/tstr/dep/container"
	"github.com/testcontainers/testcontainers-go/modules/postgres"
)

func TestMain(m *testing.M) {
	tstr.RunMain(m, tstr.WithDeps(
		container.New(
			container.WithModule(postgres.Run, "postgres:16-alpine",
				postgres.WithDatabase("test"),
			),
		),
		cmd.New(
			cmd.WithGoCode("../", "./cmd/my-app"),
			cmd.WithReadyHTTP("http://localhost:8080/ready"),
			cmd.WithGoCover(),
		),
	))
}
```

```sh
go get github.com/go-tstr/tstr@latest
```

For a fuller example with a compiled service, a Postgres container, and golden file assertions, see [go-tstr.com](https://go-tstr.com).
