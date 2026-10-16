# apokaliptium — agent & contributor guide

Sovereign Cosmos SDK blockchain of the federated Apokaliptium project
(https://www.apokaliptium.online/), scaffolded with Ignite CLI.

Upstream Ignite and Cosmos SDK docs are mostly API reference and partly
outdated after Ignite's move to gno.land. This file is the practical "how do I
run it" guide for this repository. Keep it current when something changes.

## Stack

| Thing            | Value                                   |
| ---------------- | --------------------------------------- |
| Go               | 1.26.x (`go.mod`)                       |
| Cosmos SDK       | v0.55.0                                 |
| CometBFT         | v0.40.0                                 |
| Ignite CLI       | v30.x (tested with v30.0.0-rc.2)        |
| Binary           | `apokaliptiumd` (`cmd/apokaliptiumd`)   |
| Chain ID (dev)   | `apokaliptium`                          |
| Address prefix   | `ghostshell` (`app/app.go`)             |
| Coin type        | 118                                     |
| Default denom    | `soulshard` (`config.yml`)              |
| Default home     | `~/.apokaliptium`                       |

## Ignite v30: commands moved

Ignite v30 is focused on gno.land. Its top-level `ignite chain …` and
`ignite scaffold …` commands now work with **gno realms, not this chain**.
Everything for Cosmos SDK chains lives under `ignite cosmos`:

| Old (≤ v29, still in most docs) | Now (v30)                     |
| ------------------------------- | ----------------------------- |
| `ignite chain serve`            | `ignite cosmos chain serve`   |
| `ignite chain build`            | `ignite cosmos chain build`   |
| `ignite chain init`             | `ignite cosmos chain init`    |
| `ignite scaffold module …`      | `ignite cosmos scaffold …`    |
| `ignite generate …`             | `ignite cosmos generate …`    |
| `ignite testnet …`              | `ignite cosmos testnet …`     |

When you follow an upstream tutorial, add `cosmos` after `ignite`.

## Launching

All commands run from the repository root.

### 1. Dev chain with hot reload (simplest)

```sh
ignite cosmos chain serve            # build + init + start, rebuilds on file change
ignite cosmos chain serve --reset-once   # wipe state once, e.g. after genesis changes
```

This starts one validator (the first entry in `validators:` of `config.yml`,
`alice`), creates and funds the `accounts:` from `config.yml`, and exposes the
usual endpoints (Ignite defaults):

- CometBFT RPC: `http://localhost:26657`
- REST / API: `http://localhost:1317`
- gRPC: `localhost:9090`
- Faucet: `http://localhost:4500` (account `bob`, see `faucet:`)

### 2. Build, init, then start by hand

```sh
ignite cosmos chain init             # builds, installs apokaliptiumd to $(go env GOPATH)/bin, inits ~/.apokaliptium
apokaliptiumd start                  # run the node
```

Use `--home <dir>` on both commands to keep a throwaway data directory. The
mnemonics of the dev accounts are printed by `init`; they are **dev-only**.

### 3. Plain Go (no Ignite)

```sh
go build ./...
go install ./cmd/apokaliptiumd       # same as `make install`
```

### 4. Multi-validator local testnet

`validator1` and `validator2` in `config.yml` are **not used** by
`serve`/`init`. Only the multi-node testnet uses them:

```sh
ignite cosmos testnet multi-node
```

### 5. Docker

```sh
docker compose up --build
```

(see `Dockerfile`, `compose.yaml`, `README.Docker.md`).

## Interacting with the chain

```sh
apokaliptiumd keys list --keyring-backend test
apokaliptiumd query bank balances <ghostshell1…>
apokaliptiumd tx bank send alice <ghostshell1…> 1000soulshard \
  --chain-id apokaliptium --keyring-backend test -y
apokaliptiumd status
```

## Checks

```sh
go build ./...
go vet ./...
go test ./...        # tests currently live in app/ only
make test            # govet + govulncheck + unit tests
make lint            # golangci-lint
make proto-gen       # regenerate protobuf code (needs buf)
```

CI: `.github/workflows/` (`go-unit.yml`, `lint.yml`, `lint-pr.yml`, `release.yml`).

## Layout

```
app/                 chain wiring: app.go, app_config.go (module list), genesis
cmd/apokaliptiumd/   node binary and CLI (root, commands, testnet)
proto/               protobuf + buf generation configs
docs/static/         generated openapi.json (served by the node)
testutil/            test helpers
config.yml           Ignite dev-chain config (accounts, validators, faucet)
```

Custom modules go in `x/<module>/` (create them with `ignite cosmos scaffold module <name>`).

## Known pitfalls

- **OpenTelemetry version skew.** The `buf` tool dependency pulls
  `go.opentelemetry.io/otel/log` v1.x. Every other `otel` log module
  (`otel/sdk/log`, `contrib/bridges/otelslog`, `otel/exporters/*log*`,
  `contrib/otelconf`) must match it, or the build fails with
  `undefined: log.KeyValue`. When you bump dependencies, upgrade the whole
  OTel set together, then run `go mod tidy && go build ./...`.
- The `replace` block in `go.mod` pins `sonic`, `go-libp2p`, `webtransport-go`
  and `goleveldb` for compatibility. Don't drop it without checking the build.
- After you stop a node, it may log `ERR leveldb: closed`. This is harmless.

## Workflow conventions (BOS)

- Branches: `master` → `development` → `revision` → `testing` → `releasing` →
  back to `master` through a PR. Work on `development`, never directly on `master`.
- Releases: push a `v*` tag; `release.yml` drafts the GitHub release.
- Never commit real mnemonics, keys, or node home directories.
