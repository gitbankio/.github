![Gitbank](./banner.png)

![Base Mainnet](https://img.shields.io/badge/Base_Mainnet-0052FF?style=flat-square&logo=coinbase&logoColor=white)
![Solidity](https://img.shields.io/badge/Solidity-0.8.34-363636?style=flat-square&logo=solidity&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-5.9-3178C6?style=flat-square&logo=typescript&logoColor=white)
![Node.js](https://img.shields.io/badge/Node.js-24-5FA04E?style=flat-square&logo=nodedotjs&logoColor=white)
![Claude Haiku](https://img.shields.io/badge/Claude_Haiku-NLP_Parser-D4A017?style=flat-square)
![GitHub App](https://img.shields.io/badge/GitHub_App-Bot-181717?style=flat-square&logo=github&logoColor=white)
![License](https://img.shields.io/badge/License-Apache_2.0-blue?style=flat-square)

# Gitbank

The secure on-chain bank inside your GitHub.

Gitbank gives every developer and AI agent a personal vault on Base mainnet, anchored to their GitHub identity. Assets are held as soul-bound gitTokens with no transfer or approve function - so no wallet, no agent, and no compromised key can drain the treasury.

## Try it in the Playground

> No setup required. Post a comment in the playground repo and watch the bot respond.

1. Go to [gitbankio/playground discussions](https://github.com/gitbankio/playground/discussions)
2. Open any open discussion thread (or start a new one)
3. Mention `@gitbankbot` with a command - for example:

```
@gitbankbot deposit 0.001 WETH
@gitbankbot send 10 USDC to @alice
@gitbankbot assign this task to @bob with 50 USDC bounty
```

The bot will parse your intent, execute the transaction on Base mainnet, and post back a receipt with the tx hash. Gas is covered by Gitbank.

**[Browse playground discussions](https://github.com/gitbankio/playground/discussions)**

## Command flow

```mermaid
flowchart LR
    A(["developer\nor AI agent"]) -->|"@gitbankbot assign\n@alice 80 USDC"| B["GitHub Issue / PR"]

    subgraph Gitbank
        C["webhook\nhandler"]
        D["Claude Haiku\nNLP parser"]
        E["viem relayer\nsign + submit"]
    end

    B -->|HMAC webhook| C
    C --> D
    D -->|structured intent| C
    C --> E

    subgraph "Base Mainnet"
        F["GitVault\nContract"]
        G["gitUSDC\nescrowed for @alice"]
    end

    E --> F
    F --> G

    H(["PR merged"]) -->|auto-payout trigger| C
    G -->|burn escrow\n+ release| I(["@alice\nreceives USDC"])
    C -->|receipt + tx hash| B
```

## What we build

| Repo | Description |
|------|-------------|
| [gitbankio/contracts](https://github.com/gitbankio/contracts) | Solidity smart contracts - GitVault, GitVaultFactory, soul-bound GitToken. Deployed on Base mainnet. |
| [gitbankio/server](https://github.com/gitbankio/server) | Express API server - GitHub webhook handler, Claude NLP parser, viem relayer, Drizzle ORM. |
| [gitbankio/app](https://github.com/gitbankio/app) | React + Vite frontend - onboarding, vault dashboard, connected repos. |
| [gitbankio/playground](https://github.com/gitbankio/playground) | Live sandbox - try bot commands without installing anything. |

## How it works

1. Install [@gitbankbot](https://github.com/apps/gitbankbot) on your repo
2. Deploy your vault once from the web app - one transaction, anchored to your GitHub ID
3. All commands from that point run inside GitHub issues and pull requests

Gas is covered by Gitbank. Receipt is posted back to the thread within seconds.

## Security model

- **Soul-bound GitTokens** - no transfer, no approve, no drain surface
- **GitHub Permanent User ID as identity anchor** - immutable, cannot be spoofed
- **On-chain permission enforcement** - manager roles verified at EVM level, not application level
- **Two-step commit/reveal transfers** - prevents front-running on inter-vault transfers
- **AI agent safe** - even a fully compromised agent cannot move funds without explicit on-chain permission

## Stack

| Layer | Technology |
|-------|-----------|
| Chain | Base Mainnet (L2) |
| Contracts | Solidity 0.8.34 + OpenZeppelin 5 |
| Onchain lib | viem |
| API | Express 5 + Node.js 24 |
| Database | PostgreSQL + Drizzle ORM |
| Frontend | React 19 + Vite 7 + Tailwind v4 |
| NLP | Claude Haiku (Anthropic) |
| Auth | GitHub App (webhook + OAuth) |
| Language | TypeScript 5.9 |

## License

Apache 2.0 - see [LICENSE](https://github.com/gitbankio/contracts/blob/main/LICENSE)
