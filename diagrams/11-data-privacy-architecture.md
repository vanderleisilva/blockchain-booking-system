# Data Privacy Architecture

```mermaid
graph LR
    subgraph "User Data Flow"
        A[User Personal Data] -->|1. Collect| B[CLI Input]
    end

    subgraph "Encryption Layer"
        B -->|2. Generate Key| C[Unique Key Pair]
        B -->|3. Encrypt with Key| D[Encrypted Blob]
        D -->|4. Calculate| E[SHA-256 Hash]
    end

    subgraph "Storage Separation"
        D -->|5a. Store| F[(Encrypted Data<br/>Off-Chain)]
        C -->|5b. Store Separately| G[(Encryption Keys<br/>Off-Chain)]
        E -->|5c. Store| H[(Hash Only<br/>On-Chain)]
    end

    subgraph "Verification"
        H -->|Can verify| I[Data Integrity]
        F -->|+ Keys| J[Decrypt Personal Data]
        G -->|Required for| J
    end

    subgraph "GDPR Deletion"
        K[Delete Request] -->|Delete| G
        K -->|Delete| F
        K -->|Cannot Delete| H
        H -->|Without Keys| L[Hash Useless]
    end

    style A fill:#4A90E2
    style D fill:#2ECC71
    style E fill:#E74C3C
    style F fill:#9B59B6
    style G fill:#E74C3C
    style H fill:#50C878
    style L fill:#95A5A6
```

## Description

Shows the separation of concerns:

- Personal data → Encrypted → Stored off-chain
- Encryption keys → Stored separately off-chain
- Hash → Stored on-chain
- GDPR deletion makes hash useless without keys
