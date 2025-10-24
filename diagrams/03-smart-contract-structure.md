# Smart Contract Structure (Privacy-Preserving)

```mermaid
classDiagram
    class BookingManager {
        +address owner
        +uint256 resourceCounter
        +uint256 bookingCounter
        +mapping resources
        +mapping bookings
        +createResource(name, price) uint256
        +makeBooking(resourceId, start, end, dataHash) uint256
        +cancelBooking(bookingId)
        +getResourceAvailability(resourceId, start, end) bool
        +getBooking(bookingId) Booking
        +getMyBookings() Booking[]
        +verifyBookingData(bookingId, dataHash) bool
        -checkAvailability(resourceId, start, end) bool
        -calculatePrice(resourceId, start, end) uint256
    }

    class Resource {
        +uint256 id
        +string name
        +address owner
        +uint256 pricePerDay
        +bool isActive
        +uint256 createdAt
    }

    class Booking {
        +uint256 id
        +uint256 resourceId
        +address customer
        +uint256 startDate
        +uint256 endDate
        +uint256 totalPrice
        +BookingStatus status
        +bytes32 dataHash
        +uint256 createdAt
    }

    class BookingMetadata {
        <<off-chain only>>
        +string customerName
        +string customerEmail
        +string customerPhone
        +string notes
        +string specialRequests
    }

    class BookingStatus {
        <<enumeration>>
        Pending
        Confirmed
        Cancelled
        Completed
    }

    class Ownable {
        +address owner
        +transferOwnership(newOwner)
        +onlyOwner()
    }

    class ReentrancyGuard {
        +nonReentrant()
    }

    class EncryptionService {
        <<off-chain>>
        +generateKeyPair() KeyPair
        +encrypt(data, publicKey) bytes
        +decrypt(encryptedData, privateKey) data
        +calculateHash(data) bytes32
    }

    class OffChainStorage {
        <<off-chain>>
        +storeEncryptedData(bookingId, data, keys)
        +retrieveData(bookingId) data
        +deleteKeys(bookingId) bool
        +verifyHash(bookingId, hash) bool
    }

    BookingManager *-- Resource
    BookingManager *-- Booking
    Booking --> BookingStatus
    Booking -.->|hash reference| BookingMetadata
    BookingMetadata --> OffChainStorage
    BookingMetadata --> EncryptionService
    BookingManager --|> Ownable
    BookingManager --|> ReentrancyGuard

    note for Booking "Only stores hash,\nNO personal data"
    note for BookingMetadata "Encrypted and stored\noff-chain only"
```

## Description

Class diagram showing:

- On-chain components (BookingManager, Resource, Booking with hash)
- Off-chain components (BookingMetadata, EncryptionService, OffChainStorage)
- Data structures with `dataHash` field instead of personal data
- Clear separation between public and private data
