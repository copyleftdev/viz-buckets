# Visual Guide to Leaky and Token Bucket Algorithms

This guide provides visual representations of various rate-limiting algorithms based on leaky buckets and token buckets using Mermaid charts. These algorithms control the flow of data in distributed systems by shaping traffic and preventing resource exhaustion. Each algorithm has a distinct method for managing the rate at which requests are allowed, making them crucial for ensuring system stability and preventing overload.

## 1. Leaky Bucket Algorithm

The leaky bucket algorithm controls the data flow by using a fixed-size bucket that leaks at a constant rate. Incoming packets are placed in the bucket if there’s space; otherwise, they are discarded.

```mermaid
graph TD
    subgraph Leaky_Bucket
        A[Start] --> B["Incoming packets arrive"]
        B --> C{"Is bucket full?"}
        C -->|No| D["Add packet to bucket"]
        C -->|Yes| E["Discard packet"]
        D --> F["Leak packets at a fixed rate"]
        F --> G[End]
    end

    subgraph Bucket
        H["Bucket"]
        I["Packet 1"]
        J["Packet 2"]
        K["Packet 3"]
        L["Leaking"]
    end
    
    D -.-> H
    F -.-> L

    style H fill:#f9f,stroke:#333,stroke-width:2px
    style I fill:#bbf,stroke:#333,stroke-width:2px
    style J fill:#fbf,stroke:#333,stroke-width:2px
    style K fill:#fbf,stroke:#333,stroke-width:2px
    style L fill:#bfb,stroke:#333,stroke-width:2px
```

## 2. Token Bucket Algorithm

In the token bucket algorithm, tokens are added to a bucket at a fixed rate. Requests are only allowed if there are enough tokens in the bucket, and tokens are consumed as requests pass through.

```mermaid
graph TD
    subgraph Token_Bucket
        A[Start] --> B["Tokens generated at fixed intervals"]
        B --> C{"Is bucket full?"}
        C -->|No| D["Add tokens to bucket"]
        C -->|Yes| E["Discard excess tokens"]
        D --> F["Check incoming request"]
        F --> G{"Are enough tokens available?"}
        G -->|Yes| H["Process request and consume tokens"]
        G -->|No| I["Reject request"]
        H --> J[End]
        I --> J
    end

    subgraph Bucket
        K["Bucket"]
        L["Token 1"]
        M["Token 2"]
        N["Token 3"]
        O["Request"]
    end
    
    D -.-> K
    H -.-> O

    style K fill:#f9f,stroke:#333,stroke-width:2px
    style L fill:#bbf,stroke:#333,stroke-width:2px
    style M fill:#fbf,stroke:#333,stroke-width:2px
    style N fill:#fbf,stroke:#333,stroke-width:2px
    style O fill:#bfb,stroke:#333,stroke-width:2px
```

## 3. Leaky Bucket with Queue

This variant of the leaky bucket uses a queue to hold excess packets temporarily instead of discarding them.

```mermaid
graph TD
    subgraph Queued_Leaky_Bucket
        A[Start] --> B["Incoming packets arrive"]
        B --> C{"Is bucket full?"}
        C -->|No| D["Add packet to bucket"]
        C -->|Yes| E["Add packet to queue"]
        D --> F["Leak packets at a fixed rate"]
        E --> F
        F --> G[End]
    end

    subgraph Bucket
        H["Bucket"]
        I["Packet 1"]
        J["Packet 2"]
        K["Queue"]
        L["Queued Packet"]
    end
    
    D -.-> H
    E -.-> K

    style H fill:#f9f,stroke:#333,stroke-width:2px
    style I fill:#bbf,stroke:#333,stroke-width:2px
    style J fill:#fbf,stroke:#333,stroke-width:2px
    style K fill:#bbf,stroke:#333,stroke-width:2px
    style L fill:#fbf,stroke:#333,stroke-width:2px
```

## 4. Hierarchical Token Bucket (HTB)

The hierarchical token bucket algorithm is used in scenarios where multiple rate limits are applied based on different priorities. Buckets are nested to enforce hierarchical constraints.

```mermaid
graph TD
    subgraph HTB_Algorithm
        A[Start] --> B["Generate tokens for each bucket"]
        B --> C{"Top-level bucket full?"}
        C -->|No| D["Add tokens to top-level bucket"]
        C -->|Yes| E["Add to sub-bucket based on priority"]
        E --> F["Process incoming requests"]
        D --> F
        F --> G{"Are enough tokens in respective bucket?"}
        G -->|Yes| H["Process request and consume tokens"]
        G -->|No| I["Reject or downgrade request"]
        H --> J[End]
    end

    subgraph Buckets
        K["Top-Level Bucket"]
        L["High Priority Bucket"]
        M["Medium Priority Bucket"]
        N["Low Priority Bucket"]
    end
    
    D -.-> K
    E -.-> L
    E -.-> M
    E -.-> N

    style K fill:#f9f,stroke:#333,stroke-width:2px
    style L fill:#bbf,stroke:#333,stroke-width:2px
    style M fill:#fbf,stroke:#333,stroke-width:2px
    style N fill:#f9f,stroke:#333,stroke-width:2px
```

## 5. Token Bucket with Bursting

This variant allows temporary bursts by storing extra tokens in a secondary bucket.

```mermaid
graph TD
    subgraph Bursting_Token_Bucket
        A[Start] --> B["Generate tokens at fixed intervals"]
        B --> C{"Main bucket full?"}
        C -->|No| D["Add tokens to main bucket"]
        C -->|Yes| E["Add tokens to burst bucket"]
        D --> F["Check incoming request"]
        E --> F
        F --> G{"Enough tokens in any bucket?"}
        G -->|Yes| H["Process request and consume tokens"]
        G -->|No| I["Reject request"]
        H --> J[End]
        I --> J
    end

    subgraph Buckets
        K["Main Bucket"]
        L["Burst Bucket"]
    end
    
    D -.-> K
    E -.-> L
    H -.-> K

    style K fill:#f9f,stroke:#333,stroke-width:2px
    style L fill:#bbf,stroke:#333,stroke-width:2px
```

## 6. Hybrid Leaky-Token Bucket

The hybrid leaky-token bucket algorithm combines both mechanisms, using the leaky bucket to regulate flow and the token bucket to allow short bursts.

```mermaid
graph TD
    subgraph Hybrid_Algorithm
        A[Start] --> B["Generate tokens at fixed intervals"]
        B --> C{"Leaky bucket full?"}
        C -->|No| D["Add tokens to leaky bucket"]
        C -->|Yes| E["Reject request"]
        D --> F["Check incoming request"]
        F --> G{"Enough tokens?"}
        G -->|Yes| H["Process request and consume tokens"]
        G -->|No| I["Reject request"]
        H --> J[End]
    end

    subgraph Buckets
        K["Leaky Bucket"]
        L["Token Bucket"]
    end
    
    D -.-> K
    H -.-> K

    style K fill:#f9f,stroke:#333,stroke-width:2px
    style L fill:#bbf,stroke:#333,stroke-width:2px
```

This guide provides a detailed visualization of the different variations of leaky and token bucket algorithms, making it easier to understand their mechanisms and their use cases in network rate limiting and traffic management.
