![Gitbank](./banner.png)

![Base L2](https://img.shields.io/badge/Base_L2-0052FF?style=flat-square&logo=coinbase&logoColor=white)
![Solidity](https://img.shields.io/badge/Solidity-0.8.24-363636?style=flat-square&logo=solidity&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-5.9-3178C6?style=flat-square&logo=typescript&logoColor=white)
![Node.js](https://img.shields.io/badge/Node.js-24-5FA04E?style=flat-square&logo=nodedotjs&logoColor=white)
![Claude Haiku](https://img.shields.io/badge/Claude_Haiku-NLP_Parser-D4A017?style=flat-square)
![GitHub App](https://img.shields.io/badge/GitHub_App-Bot-181717?style=flat-square&logo=github&logoColor=white)
![License](https://img.shields.io/badge/License-Apache_2.0-blue?style=flat-square)

# Gitbank

The secure on-chain bank inside your GitHub.

Gitbank gives every developer and AI agent a personal vault on Base L2, anchored to their GitHub identity. Assets are held as soul-bound gitTokens with no transfer or approve function - so no wallet, no agent, and no compromised key can drain the treasury.

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

    subgraph "Base L2"
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
| [gitbankio/contracts](https://github.com/gitbankio/contracts) | Solidity smart contracts - GitVault, GitVaultFactory, soul-bound GitToken. Deployed on Base L2. |
| [gitbankio/server](https://github.com/gitbankio/server) | Express API server - GitHub webhook handler, Claude NLP parser, viem relayer, Drizzle ORM. |
| [gitbankio/app](https://github.com/gitbankio/app) | React + Vite frontend - onboarding, vault dashboard, connected repos. |

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

Base L2 - Solidity 0.8.24 - OpenZeppelin 5 - viem - Express 5 - Drizzle ORM - React 19 - Vite 7 - Claude Haiku

## License

Apache 2.0 - see [LICENSE](https://github.com/gitbankio/contracts/blob/main/LICENSE)
