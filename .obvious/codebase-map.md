# codebase-map — myazhbin/age (filippo.io/age)

Single module `filippo.io/age`. Depth-2 overview; all paths relative to repo root.

| Path | What it is |
|---|---|
| `age.go` | Root library package `age`: file encryption/decryption, `Encrypt`/`Decrypt` APIs, v1 format entry points |
| `x25519.go` | Native `X25519` recipient/identity type (the `age1...` keys) |
| `scrypt.go` | Passphrase-based `scrypt` recipient |
| `pq.go` | Post-quantum (HPKE-based) recipient support |
| `parse.go` | Parsing helpers for recipients/identities from strings |
| `primitives.go` | Shared crypto primitives (HKDF, etc.) |
| `testkit_test.go`, `testdata/` | Conformance tests against the age test kit; sample `.age` files |
| `agessh/` | SSH keys (`ssh-rsa`, `ssh-ed25519`) as age recipients/identities |
| `armor/` | Strict, streaming ASCII armoring of age files |
| `tag/` | Tagged P-256 / P-256+ML-KEM-768 recipients for hardware-key identities |
| `plugin/` | age plugin protocol: client (`Recipient`/`Identity`) + framework for writing plugins |
| `internal/bech32/` | Bech32 encoding for `age1...` / `AGE-...` key strings |
| `internal/format/` | age file header format (parsing/serialization) |
| `internal/stream/` | STREAM chunked payload encryption (ChaCha20-Poly1305) |
| `internal/inspect/` | Header inspection used by `age-inspect` |
| `internal/term/` | Terminal I/O helpers (prompts, tty detection) |
| `cmd/age/` | Main `age` CLI (encrypt/decrypt, flags, TUI passphrases) |
| `cmd/age-keygen/` | `age-keygen` — generates X25519 key pairs |
| `cmd/age-inspect/` | `age-inspect` — human/JSON view of age file headers |
| `cmd/age-plugin-batchpass/` | Batch password plugin (non-interactive passphrase) |
| `extra/age-plugin-pq/` | `age-plugin-pq` CLI |
| `extra/age-plugin-tag/` | `age-plugin-tag` CLI |
| `extra/age-plugin-tagpq/` | `age-plugin-tagpq` CLI |
| `doc/` | Man pages: `.ronn` sources + generated `.1`/`.html` (CI regenerates on push) |
| `.github/` | Workflows (test/build/interop/ronn), CONTRIBUTING.md, issue templates |
| `logo/` | Logos and assets |
