# State Transitions

```mermaid
stateDiagram-v2
    [*] --> Pending: Booking Created
    Pending --> Confirmed: Payment Verified
    Pending --> Cancelled: User Cancels
    Confirmed --> InProgress: Start Date Reached
    InProgress --> Completed: End Date Reached
    Confirmed --> Cancelled: Early Cancellation
    Completed --> [*]
    Cancelled --> [*]

    note right of Pending
        Awaiting confirmation
        Payment held
    end note

    note right of Confirmed
        Booking active
        Cannot be modified
    end note

    note right of Completed
        Service delivered
        Payment released
    end note

    note right of Cancelled
        Refund processed
        Slot released
    end note
```

## Description

Shows the lifecycle of a booking through various states.
