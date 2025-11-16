# Distributed Storage System

A peer-to-peer key-value storage system implementation using the Actor model with Akka. This system provides distributed data storage with configurable replication, consistency guarantees, and failure handling capabilities.

## System Architecture

The system implements a distributed storage system with the following key features:

- **Actor-based Architecture**: Built using Akka actors for concurrent and distributed processing
- **Configurable Replication**: Supports N/W/R parameters for tunable consistency and availability
- **Fault Tolerance**: Handles node crashes, network partitions, and recovery scenarios
- **Quorum-based Operations**: Ensures data consistency through configurable read/write quorums
- **Dynamic Membership**: Supports joining and leaving of nodes during runtime

### Core Components

```text
src/main/java/it/unitn/ds1/
├── actors/
│   ├── StorageNode.java      # Core storage node implementation
│   └── Client.java           # Client actor for operations
├── test/
│   ├── InteractiveTest.java  # Interactive testing framework
│   └── LargeScaleTest.java   # Automated large-scale tests
├── types/
│   └── OperationType.java    # Operation type definitions
├── utils/
│   ├── VersionedValue.java   # Version-based data consistency
│   ├── TimeoutDelay.java     # Timeout management
│   └── OperationDelays.java  # Operation timing configuration 
├── Messages.java             # Actor message definitions
├── DataStoreManager.java     # Global configuration manager
└── Main.java                # Basic demo application
```

## Quick Start

### Prerequisites

- Java 21 or higher
- Gradle 9.0 or higher

### Building and Running

```bash
# Build the project
gradle build

# Run interactive testing framework (recommended)
gradle run --console=plain

# Alternative: Run specific main classes by editing build.gradle:
# - it.unitn.ds1.test.InteractiveTest (default - interactive testing)
# - it.unitn.ds1.test.LargeScaleTest (automated large-scale tests)  
# - it.unitn.ds1.Main (basic demo scenarios)
```

### Building and Running

```bash
# Build the project
gradle build

# Run interactive testing framework (recommended)
gradle run --console=plain

# Alternative: Run specific main classes by editing build.gradle
# - it.unitn.ds1.test.InteractiveTest (default - interactive testing)
# - it.unitn.ds1.test.LargeScaleTest (automated large-scale tests)  
# - it.unitn.ds1.Main (basic demo scenarios)
```

## Key Features

### Distributed Key-Value Storage

- **N Replication**: Keys are distributed across N nodes using consistent hashing
- **Versioned Values**: Each value is versioned to handle concurrent updates
- **Atomic Operations**: Read and write operations are atomic at the key level

### Configurable Consistency Model

The system uses N/W/R parameters:

- **N**: Total number of replicas per key (default: 3)
- **W**: Number of nodes that must acknowledge a write (default: 2)
- **R**: Number of nodes that must respond to a read (default: 2)

Default configuration provides strong consistency with R + W > N.

### Fault Tolerance

- **Node Crashes**: System continues operating when nodes fail
- **Network Partitions**: Handles network splits gracefully
- **Timeout Handling**: Configurable timeouts for operations
- **Recovery**: Crashed nodes can rejoin and synchronize data

### Dynamic Membership

- **Join Protocol**: New nodes can join the network dynamically
- **Leave Protocol**: Nodes can gracefully leave, transferring their data
- **Data Repartitioning**: Automatic data redistribution on membership changes

## Interactive Testing Framework

The system includes a comprehensive interactive testing framework accessible via `InteractiveTest.java`.

### Command Reference

#### System Information

```bash
### Command Reference

#### System Information
```bash
help, h                  # Show help message
status, s                # Show system status  
nodes                    # List all nodes and their states
clients                  # List all clients
data                     # Show data distribution
```

#### Basic Operations
```bash
get <key> [nodeId] [clientId]         # Read value for key
put <key> <value> [nodeId] [clientId] # Store key-value pair
```

#### Node Management
```bash
addnode [nodeId]         # Add a new node to the system
removenode <nodeId>      # Remove a node from the system
crash <nodeId>           # Crash a specific node
recover <nodeId> [peerNodeId] # Recover a crashed node
join <nodeId> <peerNodeId>    # Join a node via bootstrap peer
leave <nodeId>           # Node graceful departure
```

#### Client Management
```bash
addclient                # Add a new client actor
removeclient <clientId>  # Remove a client actor
```

#### Testing Scenarios
```bash
test basic               # Test basic CRUD operations
test quorum              # Test quorum behavior with failures
test concurrency        # Test concurrent operations
test consistency        # Test data consistency across nodes
test membership         # Test join/leave operations
test partition          # Test network partition scenarios
test recovery           # Test crash recovery scenarios
test edge-cases          # Test edge cases and error conditions
test timeout             # Test client timeout scenarios
```

#### Performance Testing
```bash
benchmark <operations>   # Run performance benchmark
stress <duration>        # Run stress test for specified seconds
```

#### System Control
```bash
reset                    # Reset entire system to initial state
clear                    # Clear screen
quit, exit, q            # Exit the system
```

## Configuration

### System Parameters

Configuration is managed by `DataStoreManager` and set at system initialization:

```java
Configuration is managed by `DataStoreManager` and set at system initialization:

```java
// Default configuration
N = 3  // Number of replicas
W = 2  // Write quorum size  
R = 2  // Read quorum size
```
```

### Timeout Configuration

Timeouts are defined in `TimeoutDelay.java`:

```java
CLIENT_GET_DELAY = BASE_DELAY * 4.5      // ~2025ms
CLIENT_UPDATE_DELAY = BASE_DELAY * 4.75  // ~2137ms  
JOIN_DELAY = BASE_DELAY * 6.2            // ~2790ms
```

### Logging

The system uses Logback for structured logging. Configuration can be modified in `src/main/resources/logback.xml` to adjust log levels for different components:

```xml
The system uses Logback for structured logging. Configuration can be modified in `src/main/resources/logback.xml` to adjust log levels for different components:

- StorageNode operations
- Client requests and responses  
- System membership changes
- Error conditions and timeouts

## Performance Characteristics
```

## Advanced Usage

### Custom Test Scenarios

You can create custom test scenarios by extending the testing framework:

```java
// Example: Custom consistency test
private static void customConsistencyTest() {
    // Your test implementation
    ActorRef client = getRandomClient();
    ActorRef coordinator = getRandomActiveNode();
    
    // Perform operations and validate consistency
    client.tell(new Messages.InitiateUpdate(key, value, coordinator), ActorRef.noSender());
    // ... validation logic
}
```

### Configuration Tuning

Modify `DataStoreManager` initialization for different consistency models:

```java
// Strong consistency (default)
DataStoreManager.initialize(3, 2, 2);

// Eventual consistency  
DataStoreManager.initialize(3, 1, 1);

// Custom configuration
DataStoreManager.initialize(N, W, R);
```

## Performance Characteristics

### Throughput

- **Write Operations**: Depends on W (write quorum size)
- **Read Operations**: Depends on R (read quorum size)  
- **Network Overhead**: O(N) messages per operation

### Latency

- **Best Case**: Single round-trip to coordinator
- **Worst Case**: Multiple rounds for quorum satisfaction
- **Timeout Handling**: Configurable per operation type

### Scalability

- **Horizontal Scaling**: Add nodes dynamically
- **Load Distribution**: Even key distribution via hashing
- **Fault Tolerance**: System operates with up to (N-W) node failures

## Technical Documentation

### Consistency Model

The system implements a quorum-based consistency model where:
- Strong consistency: R + W > N
- Eventual consistency: R + W ≤ N

### Data Distribution

Keys are distributed using consistent hashing:
1. Find the first node with ID ≥ key
2. Select N consecutive nodes starting from that position
3. Handle wrap-around for ring topology

### Concurrency Control

Two-level locking mechanism:
- **Coordinator Level**: Prevents conflicting operations on the same key
- **Replica Level**: Controls concurrent access at individual replicas (reader-writer semantics)

## References

- Akka Documentation: https://akka.io/docs/
- Distributed Systems Concepts: Consistency, Availability, Partition Tolerance, Quorum Systems

## License

Academic project for distributed systems coursework.
