# System Architecture Diagram

```mermaid
graph TB
    subgraph "User Interface Layer"
        CLI[CLI Application<br/>Node.js + TypeScript]
        CLI_CMD[Command Handlers<br/>wallet, resource, booking, privacy]
    end

    subgraph "Application Layer"
        WalletMgr[Wallet Manager<br/>Wallet Key Management]
        BlockchainSvc[Blockchain Service<br/>Web3/Ethers.js]
        EncryptSvc[Encryption Service<br/>AES-256-GCM + Key Gen]
        StorageSvc[Storage Service<br/>Off-Chain Data Manager]
        ConfigMgr[Config Manager<br/>Network Settings]
    end

    subgraph "Blockchain Layer"
        RPC[JSON-RPC Provider<br/>Ethereum Node]

        subgraph "Smart Contracts"
            BookingMgr[BookingManager Contract<br/>Stores ONLY Hashes]
            AccessCtrl[Access Control<br/>Permissions]
        end

        Blockchain[(Blockchain<br/>Public Ledger)]
    end

    subgraph "Off-Chain Storage Layer"
        EncryptDB[(Encrypted Data Store<br/>Personal Information)]
        KeyVault[(Key Vault<br/>Encryption Keys)]
        PrivacyAPI[Privacy API<br/>GDPR Operations]
    end

    subgraph "Storage"
        LocalDB[Local Storage<br/>Wallets & Config]
        BCStorage[On-Chain Storage<br/>Hashes Only, NO PII]
    end

    CLI --> CLI_CMD
    CLI_CMD --> WalletMgr
    CLI_CMD --> BlockchainSvc
    CLI_CMD --> EncryptSvc
    CLI_CMD --> StorageSvc
    CLI_CMD --> ConfigMgr

    WalletMgr --> LocalDB
    ConfigMgr --> LocalDB

    EncryptSvc --> StorageSvc
    StorageSvc --> PrivacyAPI

    PrivacyAPI --> EncryptDB
    PrivacyAPI --> KeyVault

    BlockchainSvc --> RPC
    BlockchainSvc -.->|Only sends hash| BookingMgr
    RPC --> BookingMgr
    RPC --> AccessCtrl

    BookingMgr --> Blockchain
    AccessCtrl --> BookingMgr

    Blockchain --> BCStorage

    style CLI fill:#4A90E2
    style BookingMgr fill:#E24A4A
    style Blockchain fill:#50C878
    style LocalDB fill:#FFB84D
    style BCStorage fill:#FFB84D
    style EncryptDB fill:#9B59B6
    style KeyVault fill:#E74C3C
    style EncryptSvc fill:#2ECC71
    style PrivacyAPI fill:#9B59B6
```

## Description

Shows the complete system architecture with privacy layers:

- **User Interface Layer**: CLI application with privacy command handlers
- **Application Layer**: Business logic, blockchain interaction, and encryption services
- **Blockchain Layer**: Smart contracts storing only cryptographic hashes
- **Off-Chain Storage Layer**: Encrypted personal data storage with separate key management

**Key Privacy Features:**

- Personal data never reaches the blockchain
- Encryption keys stored separately from encrypted data
- Privacy API handles GDPR operations
