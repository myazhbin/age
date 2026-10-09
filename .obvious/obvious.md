# obvious.md — myazhbin/age (filippo.io/age)

**What this is:** age — a simple, modern and secure file encryption tool, format, and Go
library (spec: age-encryption.org/v1). Pure Go, no cgo required. Ships four CLI commands
and a Go library, plus plugin helpers. No web server, no database, no external services,
no environment variables.

## Stack

- **Language:** Go — `go.mod` requires `go 1.25.0`, toolchain pinned `go1.27.0`
  (see `go.mod`; CI runs stable + oldstable).
- **Package manager:** Go modules (`go.mod` / `go.sum`). No vendor dir.
- **Build system:** none (no Makefile, no Dockerfile/Compose). Plain `go` commands.
- **CI (`.github/workflows/`):** `test.yml` runs `go test -race ./...`, staticcheck,
  govulncheck; `build.yml` builds `./cmd/... ./extra/...` for release binaries.

## Environment setup (this sandbox)

Go is NOT preinstalled. It is installed in the snapshot at `$HOME/go-dist/go` (go1.27.0).
Always prepend it to PATH before any go command:

```sh
export PATH=$HOME/go-dist/go/bin:$PATH
```

If the install is missing, restore it:

```sh
curl -fsSL -o /tmp/go.tgz https://go.dev/dl/go1.27.0.linux-amd64.tar.gz \
  && mkdir -p $HOME/go-dist && tar -C $HOME/go-dist -xzf /tmp/go.tgz
```

No other dependencies: no services to start, no env vars, no secrets, no migrations.

## Commands

```sh
export PATH=$HOME/go-dist/go/bin:$PATH   # required first

go build ./...                # build everything (fast, <1 min)
go vet ./...                  # vet (CI-equivalent lint baseline)
gofmt -l .                    # must print nothing — repo is gofmt-clean
go test -race ./...           # canonical test suite (CI uses exactly this); ~3.5 min
go test -race ./... -run TestX -count=1   # scoped, during iteration
go run ./cmd/age ...          # run the CLI from source
go build -o BIN/ ./cmd/age ./cmd/age-keygen ./cmd/age-inspect ./cmd/age-plugin-batchpass ./extra/...
```

Binaries built from `cmd/` and `extra/`: `age`, `age-keygen`, `age-inspect`,
`age-plugin-batchpass`, `age-plugin-pq`, `age-plugin-tag`, `age-plugin-tagpq`.

## Primary user flow (use as smoke test)

```sh
age-keygen -o key.txt                       # prints Public key: age1...
age -r <PUBKEY> -o out.age file.txt         # encrypt to an X25519 recipient
age --decrypt -i key.txt -o back.txt out.age
diff file.txt back.txt                      # must be empty
age -r <PUBKEY> -a -o out.age file.txt      # -a = ASCII armor variant
age -d -i key.txt out.age                   # decrypt armored
age-inspect out.age                         # human-readable header breakdown
```

## Codebase map

See `.obvious/codebase-map.md` (single table, depth 2).

## Local verification (definition of done)

1. `go build ./...` succeeds; `go vet ./...` clean; `gofmt -l .` empty.
2. `go test -race ./...` exits 0 (all packages ok; `internal/stream` alone ~3 min).
3. Primary flow above round-trips (plain + armor) with `diff` empty.
4. `age-inspect` parses an encrypted file without error.

## Snapshot

- **snapshotId:** `sircemwv1nrv9zj85cjn:default`
- **captured:** 2026-10-09T15:58:25Z (ISO-8601)
- **state captured:** repo at `main` clean checkout + Go 1.27.0 installed at
  `$HOME/go-dist/go` + warm module cache. Restoring the snapshot reproduces the
  working dev environment.

## Notes & conventions

- Upstream maintenance policy (`.github/CONTRIBUTING.md`): changes may be reimplemented
  rather than merged; keep style consistent, complexity minimal.
- No `.gitignore` in this repo; `.obvious/` is intentionally committed.
- `TODO(confirm)`: none — all commands above were executed successfully during onboarding.
