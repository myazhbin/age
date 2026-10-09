---
name: local-dev
description: How to build, test, and exercise filippo.io/age locally in this sandbox
---

# local-dev — myazhbin/age (filippo.io/age)

Durable record of the LOCAL-DEV onboarding run (2026-10-09). Everything below was
executed successfully; this is the fastest reliable path to a working dev environment.

## Environment facts (learned the hard way)

- Debian 13 (trixie) x86_64 sandbox. **Go is not preinstalled** and there is no
  Makefile/Dockerfile — install the toolchain yourself.
- go.mod: `go 1.25.0`, `toolchain go1.27.0`. Install exactly go1.27.0 to match.
- gcc is present, so `-race` works (CI runs `go test -race ./...` — use the same).
- Module deps download on first build (network needed); cache is warm in the snapshot.

## Setup (if snapshot was not restored)

```sh
curl -fsSL -o /tmp/go.tgz https://go.dev/dl/go1.27.0.linux-amd64.tar.gz \
  && mkdir -p $HOME/go-dist && tar -C $HOME/go-dist -xzf /tmp/go.tgz
export PATH=$HOME/go-dist/go/bin:$PATH   # every shell, every time
```

## Verify the stack (in order)

```sh
export PATH=$HOME/go-dist/go/bin:$PATH
go build ./...      # BUILD_OK expected, <1 min
go vet ./...        # VET_OK expected
gofmt -l .          # expect zero output
go test -race ./... # exit 0; ~3.5 min total (internal/stream ~3 min of it)
```

Observed test times: `internal/stream` 202s, root `age` 44s, `cmd/age` 29s,
`agessh` 7s, everything else <2s. Total run 216s on this sandbox.

## Primary flow (end-to-end smoke test)

```sh
go build -o /tmp/age-bin/ ./cmd/age ./cmd/age-keygen ./cmd/age-inspect ./cmd/age-plugin-batchpass
cd $(mktemp -d)
/tmp/age-bin/age-keygen -o key.txt              # capture "Public key: age1..."
age -r $PUBKEY -o secret.age secret.txt         # encrypt
/tmp/age-bin/age --decrypt -i key.txt -o back.txt secret.age
diff secret.txt back.txt                        # empty = ROUNDTRIP_OK
/tmp/age-bin/age -r $PUBKEY -a -o a.age secret.txt   # armor variant
/tmp/age-bin/age -d -i key.txt a.age | diff secret.txt -
/tmp/age-bin/age-inspect secret.age             # parses header, prints breakdown
```

## Gotchas

- `go test -race ./...` is slow but is exactly what CI runs; don't "optimize" it
  into `-p` subsets when verifying.
- There are no env vars, no ports, no services — if something seems to need one,
  you're looking at the wrong repo state.
- `doc/` man pages are CI-generated from `.ronn` sources; don't hand-edit `.1`/`.html`.
- Upstream maintainer policy: PRs may be reimplemented rather than merged
  (see `.github/CONTRIBUTING.md`).

## Evidence captured during onboarding (2026-10-09)

- `go build ./...` → BUILD_OK; `go vet ./...` → VET_OK; `gofmt -l .` → 0 files.
- `go test -race ./...` → all packages `ok`, exit 0.
- E2E: keygen → encrypt → decrypt round-trip OK; armor round-trip OK; inspect OK.
  Public key used: `age16xufapsthufjuqk84jswqxvm6r3v0sjp53znghx5lslq4c8vqc9q06nszu`.
