# Privacy & Encryption Flow

```mermaid
flowchart TD
    Start([User Makes Booking]) --> Collect[Collect Personal Data]
    Collect --> Generate[Generate Unique Key Pair<br/>per Booking]

    Generate --> Encrypt[Encrypt Personal Data<br/>AES-256-GCM]
    Encrypt --> Hash[Calculate Hash<br/>SHA-256]

    Hash --> StoreEncrypted[Store Encrypted Data<br/>Off-Chain]
    Hash --> StoreKeys[Store Keys Separately<br/>Off-Chain]
    Hash --> StoreHash[Store Hash Only<br/>On Blockchain]

    StoreEncrypted --> Complete{Booking<br/>Complete}
    StoreKeys --> Complete
    StoreHash --> Complete

    Complete -->|Later| Retrieve[User Retrieves Data]
    Complete -->|Or| Delete[GDPR Delete Request]

    Retrieve --> FetchKeys[Fetch Keys from Vault]
    FetchKeys --> FetchData[Fetch Encrypted Data]
    FetchData --> Decrypt[Decrypt with Private Key]
    Decrypt --> Display[Display Personal Data]
    Display --> End1([End])

    Delete --> DelKeys[Delete Encryption Keys]
    Delete --> DelData[Delete Encrypted Data]
    DelKeys --> Permanent[Data Permanently Lost]
    DelData --> Permanent
    Permanent --> HashRemains[Hash Remains on Chain<br/>But Useless]
    HashRemains --> End2([GDPR Compliant])

    style Start fill:#50C878
    style Encrypt fill:#2ECC71
    style Hash fill:#E74C3C
    style StoreHash fill:#FFB84D
    style Delete fill:#E74C3C
    style Permanent fill:#95A5A6
    style End2 fill:#2ECC71
```

## Description

Complete flowchart showing:

- Booking creation with encryption
- Data retrieval with decryption
- GDPR deletion process
- How hash becomes useless after key deletion
