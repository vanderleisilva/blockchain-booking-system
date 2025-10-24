# Privacy-Preserving Booking Flow Sequence Diagram

```mermaid
sequenceDiagram
    actor User
    participant CLI
    participant Encrypt as Encryption Service
    participant Storage as Off-Chain Storage
    participant Wallet
    participant Web3
    participant Contract
    participant Blockchain

    User->>CLI: booking create --resource 1<br/>--from 2025-11-01 --to 2025-11-03
    CLI->>CLI: Parse arguments
    CLI->>User: Prompt for personal data<br/>(name, email, phone)
    User->>CLI: Provide personal info

    Note over CLI,Encrypt: PRIVACY LAYER - Encryption
    CLI->>Encrypt: Generate unique key pair
    Encrypt-->>CLI: Public/Private keys
    CLI->>Encrypt: Encrypt personal data
    Encrypt-->>CLI: Encrypted data blob
    CLI->>Encrypt: Calculate SHA-256 hash
    Encrypt-->>CLI: dataHash

    Note over CLI,Storage: OFF-CHAIN STORAGE
    CLI->>Storage: Store encrypted data + keys
    Storage-->>CLI: Storage confirmed

    Note over CLI,Blockchain: BLOCKCHAIN LAYER - Only Hash
    CLI->>Wallet: Request signature authorization
    Wallet-->>CLI: Provide account

    CLI->>Web3: Check resource availability
    Web3->>Contract: getResourceAvailability(1, startDate, endDate)
    Contract->>Blockchain: Read state
    Blockchain-->>Contract: Return availability data
    Contract-->>Web3: Available: true
    Web3-->>CLI: Resource available

    CLI->>User: Display booking details & cost<br/>Prompt for confirmation
    User->>CLI: Confirm

    CLI->>Web3: Estimate gas
    Web3-->>CLI: Gas estimate: 150,000

    CLI->>Wallet: Sign transaction
    Wallet-->>CLI: Signed transaction

    CLI->>Web3: Send transaction<br/>makeBooking(1, startDate, endDate, dataHash)
    Note right of Web3: Only hash sent,<br/>NO personal data!
    Web3->>Contract: Execute makeBooking

    Contract->>Contract: Validate dates
    Contract->>Contract: Check availability
    Contract->>Contract: Check payment
    Contract->>Contract: Store booking + dataHash
    Contract->>Blockchain: Write hash to chain

    Blockchain-->>Contract: Transaction confirmed
    Contract->>Contract: Emit BookingCreated(id, dataHash)
    Note right of Contract: Event contains<br/>NO personal data
    Contract-->>Web3: Transaction receipt
    Web3-->>CLI: Receipt with booking ID

    CLI->>User: ✅ Booking successful!<br/>Booking ID: 42<br/>Transaction: 0xabc123...<br/>Personal data encrypted & stored off-chain
```

## Description

Illustrates the complete privacy-preserving flow when a user makes a booking:

1. Personal data collected from user
2. Data encrypted with unique key pair
3. Hash calculated from encrypted data
4. Encrypted data stored off-chain
5. Only hash sent to blockchain
6. No personal data in blockchain events
