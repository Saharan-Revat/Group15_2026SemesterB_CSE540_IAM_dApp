# Decentralized Identity and Access Management (IAM) dApp

**CSE 540: Engineering Blockchain Applications — Group 15**

> Revat Saharan · Abhilakshay Sreehari · Satwik Mazumdar · Sreehari Krishna Sadesh · Shashank Valayaputtur

---

## Project Description

This project implements a **Self-Sovereign Identity (SSI)** system on the Ethereum/Polygon blockchain, following the [W3C Decentralized Identifier (DID)](https://www.w3.org/TR/did-core/) and [Verifiable Credentials (VC)](https://www.w3.org/TR/vc-data-model/) specifications.

Traditional identity management relies on centralized authorities (governments, universities, corporations), creating single points of failure, privacy risks, and lack of user control. This dApp removes those intermediaries by enabling users to:

- **Own** their digital identity via a cryptographically secured DID.
- **Receive** verifiable credentials from trusted issuers (e.g., a university degree, a KYC certificate).
- **Selectively disclose** credentials to verifiers — sharing only what is needed, nothing more.
- **Revoke** consent and credentials at any time.

No raw personally identifiable information (PII) is ever stored on-chain.

---

## System Architecture

```
┌──────────────────────────────────────────────────────────┐
│                      Application Layer                    │
│  React.js Frontend + MetaMask + Web3.js / Ethers.js       │
│  ┌──────────────┐  ┌───────────────┐  ┌───────────────┐  │
│  │ Holder Wallet│  │ Issuer Portal │  │Verifier Dashbd│  │
│  └──────────────┘  └───────────────┘  └───────────────┘  │
└──────────────────────────────────────────────────────────┘
              │                         │
              ▼                         ▼
┌──────────────────────────────────────────────────────────┐
│                    Blockchain Layer (On-Chain)            │
│  ┌──────────────┐  ┌──────────────────┐  ┌───────────┐  │
│  │ DIDRegistry  │  │ CredentialStatus │  │AccessCtrl │  │
│  │   .sol       │  │     .sol         │  │  .sol     │  │
│  └──────────────┘  └──────────────────┘  └───────────┘  │
└──────────────────────────────────────────────────────────┘
              │                         │
              ▼                         ▼
┌──────────────────────────────────────────────────────────┐
│                    Off-Chain Storage (IPFS)               │
│   Encrypted VC payloads pinned via CID.                  │
│   On-chain: only the CID + keccak256 hash anchor.        │
└──────────────────────────────────────────────────────────┘
```

### Smart Contracts

| Contract | Responsibility |
|---|---|
| `DIDRegistry.sol` | Registers DIDs, stores public keys and service endpoints, handles deactivation |
| `CredentialStatus.sol` | Issues credential anchors (hash + IPFS CID), manages bitmap-based revocation |
| `AccessControl.sol` | Manages stakeholder roles (Holder, Issuer, Verifier) and per-credential consent grants |

### Identity Lifecycle

1. **Registration** — User generates a DID and anchors minimal metadata (public key, service endpoint) on-chain via `DIDRegistry`.
2. **Issuance** — A trusted Issuer signs a VC off-chain (EIP-712), encrypts it, pins it to IPFS, then writes the IPFS CID and credential hash to `CredentialStatus`.
3. **Presentation & Verification** — The Holder grants selective consent to a Verifier. The Verifier fetches the encrypted VC from IPFS, decrypts it, recomputes the hash, compares it to the on-chain commitment, and checks the revocation bitmap — all without the Issuer's involvement.

---

## Repository Structure

```
iam-dapp/
├── contracts/
│   ├── DIDRegistry.sol         # DID registration and resolution
│   ├── CredentialStatus.sol    # Credential anchoring and revocation (bitmap)
│   └── AccessControl.sol       # Role management and consent registry
├── scripts/
│   └── deploy.js               # Deployment script (Hardhat)
├── test/
│   └── iam.test.js             # Unit test scaffold (Chai + Hardhat)
├── docs/
│   └── architecture.md         # Detailed architecture notes (in progress)
├── hardhat.config.js           # Hardhat configuration (Solidity 0.8.20)
├── package.json
└── README.md
```

---

## Dependencies

| Tool | Version | Purpose |
|---|---|---|
| Node.js | >= 18.x | JavaScript runtime |
| Hardhat | ^2.19.0 | Ethereum development environment |
| Solidity | 0.8.20 | Smart contract language |
| Ethers.js | ^6.9.0 | Blockchain interaction library |
| MetaMask | Latest | Browser wallet for frontend |
| IPFS / web3.storage | Latest | Decentralized off-chain storage |
| React.js | ^18.x | Frontend framework *(Phase 3)* |

---

## Setup & Installation

### Prerequisites
- [Node.js](https://nodejs.org/) (v18 or higher)
- [MetaMask](https://metamask.io/) browser extension
- A funded testnet wallet (Sepolia ETH or Mumbai MATIC)

### Install Dependencies

```bash
git clone https://github.com/YOUR_USERNAME/iam-dapp.git
cd iam-dapp
npm install
```

### Compile Contracts

```bash
npm run compile
# or: npx hardhat compile
```

### Run Tests

```bash
npm test
# or: npx hardhat test
```

### Deploy Locally (Hardhat Network)

```bash
# Terminal 1 — start local blockchain node
npm run node

# Terminal 2 — deploy contracts
npm run deploy:local
```

### Deploy to Testnet

1. Create a `.env` file in the project root:

```env
PRIVATE_KEY=your_wallet_private_key_here
SEPOLIA_RPC_URL=https://sepolia.infura.io/v3/YOUR_INFURA_KEY
MUMBAI_RPC_URL=https://rpc-mumbai.maticvigil.com
ETHERSCAN_API_KEY=your_etherscan_api_key
```

2. Deploy:

```bash
npm run deploy:sepolia    # Ethereum Sepolia
# or
npm run deploy:mumbai     # Polygon Mumbai (lower gas)
```

---

## Current Status (Smart Contract Design Draft)

This repository represents the **Smart Contract Design Draft** milestone. The current state includes:

- [x] Project architecture defined
- [x] All three smart contracts drafted with function signatures and interfaces
- [x] High-level NatSpec comments on all functions
- [x] Deployment script structured
- [x] Test scaffold with planned test cases
- [ ] Function body implementations *(in progress — Midterm Update)*
- [ ] Frontend integration *(Phase 3)*
- [ ] IPFS integration *(Phase 3)*
- [ ] Gas optimization analysis *(Final Project)*

---

## Team

| Name | Role |
|---|---|
| Revat Saharan | Lead Smart Contract Developer |
| Abhilakshay Singh Pathania | Web3 & Frontend Developer |
| Satwik Mazumdar | Blockchain Analyst & QA |
| Sreehari Krishna Sadesh | Decentralized Data Engineer |
| Shashank Valayaputtur | Systems Integration & Testing |

**Submitter:** Satwik Mazumdar

---

## References

- W3C DID Core Spec: https://www.w3.org/TR/did-core/
- W3C Verifiable Credentials: https://www.w3.org/TR/vc-data-model/
- Ethereum Smart Contracts: https://ethereum.org/en/developers/docs/smart-contracts/
- IPFS Documentation: https://docs.ipfs.tech/
- Polygon Architecture: https://wiki.polygon.technology/
