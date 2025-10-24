# GDPR "Right to Be Forgotten" Flow

```mermaid
sequenceDiagram
    actor User
    participant CLI
    participant Storage as Off-Chain Storage
    participant KeyVault as Key Vault
    participant Blockchain

    User->>CLI: booking forget --booking-id 42
    CLI->>User: ⚠️ Warning: This will permanently<br/>delete your personal data!<br/>Confirm? (yes/no)
    User->>CLI: yes

    Note over CLI,Storage: STEP 1: Verify Ownership
    CLI->>Blockchain: getBooking(42)
    Blockchain-->>CLI: Booking data (customer address)
    CLI->>CLI: Verify user owns booking

    Note over Storage,KeyVault: STEP 2: Delete Encryption Keys
    CLI->>KeyVault: deleteKeys(bookingId: 42)
    KeyVault->>KeyVault: Mark keys as deleted
    KeyVault->>KeyVault: Securely wipe private key
    KeyVault-->>CLI: Keys deleted

    Note over Storage: STEP 3: Delete Encrypted Data
    CLI->>Storage: deleteEncryptedData(bookingId: 42)
    Storage->>Storage: Remove encrypted personal data
    Storage-->>CLI: Data deleted

    Note over Blockchain: STEP 4: Hash Remains On-Chain
    Note right of Blockchain: Hash still exists but is<br/>now mathematically useless<br/>without the encryption key

    CLI->>User: ✅ Personal data deleted!<br/>Booking hash remains on blockchain<br/>but personal data is irretrievable

    Note over User,Blockchain: GDPR Compliance Achieved:<br/>✓ Data effectively deleted<br/>✓ Blockchain integrity maintained<br/>✓ Hash proves booking existed<br/>✗ No way to recover personal info
```

## Description

Demonstrates how personal data deletion works:

1. User requests deletion
2. System verifies ownership
3. Encryption keys are securely wiped
4. Encrypted data is deleted
5. On-chain hash remains but becomes useless
6. GDPR compliance achieved while maintaining blockchain integrity
