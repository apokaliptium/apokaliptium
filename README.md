# ` Apokaliptium `

### _A karma-backed blockchain token for Miracle Children_

> A philosophical and financial experiment: a token whose supply equals the
> estimated undisturbed human population in the year 3025. If we perish, it was
> just another speculative bubble. If we survive, the tokens stay abandoned as an
> act of "no regrets."

This repository holds the **Apokaliptium chain**: a sovereign blockchain built
with the [Cosmos SDK](https://docs.cosmos.network) and
[CometBFT](https://cometbft.com), scaffolded with
[Ignite CLI](https://ignite.com/cli). It is part of the federated Apokaliptium
project, [apokaliptium.online](https://www.apokaliptium.online/).

The original concept paper is in [`.github/README.md`](.github/README.md).

---

## What it is and why

Apokaliptium ties a financial asset to the **continuation of civilization**.
That turns our collective fate into an economic paradox and an open
meta-experiment in how people assign value.

Planned uses (from the concept):

- **Karma-based scoring.** You earn tokens by contributing to digital
  discourse: logic, engagement, ethical debate.
- **Proof-of-Existence ledger.** Transactions become permanent records, linked
  to decentralized archives (Git over Tor, Omnilibris torrents) and verified
  with OpenTimestamps.
- **Commerce.** An incentive for purchases and contributions on
  [Bonescraft.pl](https://bonescraft.pl).
- **Governance.** Staking to influence decisions of the *United Congregation of
  Digital Intellects*.
- **Consensus.** A hybrid of **Proof of Continuity** and **Proof of Existence**,
  layered on top of the chain's base consensus.

> **Note:** the concept paper names Solana as the platform. The implementation
> has since moved to its own Cosmos SDK chain, so the project controls its
> consensus, modules and governance.

## Current state

**Early stage.** This is a working base chain with the standard Cosmos SDK
modules (bank, staking, governance, …). None of the Apokaliptium-specific
logic (karma, PoC/PoE, the 3025 supply) is implemented yet.

| Property          | Value                    |
| ----------------- | ------------------------ |
| Binary            | `apokaliptiumd`          |
| Chain ID (dev)    | `apokaliptium`           |
| Address prefix    | `ghostshell…`            |
| Native denom      | `soulshard`              |
| Cosmos SDK        | v0.55                    |
| CometBFT          | v0.40                    |

## Quickstart

Requirements: [Go](https://go.dev/dl/) 1.26+ and
[Ignite CLI](https://docs.ignite.com) v30+.

```sh
git clone https://github.com/apokaliptium/apokaliptium.git
cd apokaliptium
ignite cosmos chain serve
```

This builds the chain, creates the dev accounts from `config.yml` (`alice`,
`bob`), starts one validator node, and rebuilds it whenever the code changes.
Once it is running:

- RPC: http://localhost:26657
- REST API: http://localhost:1317
- Faucet: http://localhost:4500

> **Ignite v30 changed its commands.** Ignite now focuses on gno.land, so its
> plain `ignite chain …` and `ignite scaffold …` commands **don't work** for
> this project. Use `ignite cosmos chain …` and `ignite cosmos scaffold …`
> instead. Many upstream tutorials still show the old form.

Without Ignite:

```sh
make install          # installs apokaliptiumd into $(go env GOPATH)/bin
apokaliptiumd --help
```

With Docker:

```sh
docker compose up --build
```

For every launch mode (manual init, multi-validator testnet), CLI examples,
checks and known pitfalls, see **[AGENTS.md](AGENTS.md)**. It is written for AI
agents and humans alike.

## Configuration

`config.yml` sets up the development chain: accounts and their starting
balances, validators, the faucet and the default denom. Changing genesis-related
values requires resetting state:

```sh
ignite cosmos chain serve --reset-once
```

## Development

```sh
go test ./...         # unit tests
make test             # vet + govulncheck + unit tests
make lint             # golangci-lint
make proto-gen        # regenerate protobuf code
ignite cosmos scaffold module <name>   # add a custom module under x/
```

## Roadmap

1. **Final supply calculation**: set the 3025 population projection from the
   most reliable demographic data.
2. **Custom modules**: karma scoring and the PoC/PoE validation logic.
3. **Integrations**: Bonescraft.pl, the debate system, OpenTimestamps
   anchoring.
4. **Publication & public shockwave.**
5. **Monitor and observe the madness unfold.**

## Releases

Pushing a `v*` tag triggers `.github/workflows/release.yml`, which drafts a
GitHub release with prebuilt binaries:

```sh
git tag v0.1.0
git push origin v0.1.0
```

Once a release is published, the node can be installed with:

```sh
curl https://get.ignite.com/apokaliptium/apokaliptium@latest! | sudo bash
```

## Links

- Project: https://www.apokaliptium.online/
- GitHub repositories: https://github.com/apokaliptium
- Author: [Sett Sarverott](https://sett.sarverott.com)
- Cosmos SDK docs: https://docs.cosmos.network
- Ignite CLI docs: https://docs.ignite.com

---

_by Sett Sarverott, from the Inceptorium Apokryf._

# **` Welcome to the new frontier of speculative existentialism. `**
