# Backend Engineering - Booking System
## Master Learning Roadmap

> Goal: Learn backend engineering by building and progressively scaling a booking system.
>
> Learning loop:
> Problem -> Requirements -> Reasoning -> Pseudocode -> Implementation -> Testing -> Debugging -> Optimization -> Notes

---

# Phase 0 - Project & Engineering Setup

## 0.1 Define the Booking System
- Product requirements
- Actors
- Core use cases
- Booking lifecycle
- Availability lifecycle
- Business rules
- Failure scenarios
- Non-functional requirements

## 0.2 Domain Modeling
- User
- Listing / Property
- Availability
- Booking
- Review
- Payment concept
- Notification concept

## 0.3 Development Environment
- Git repository
- Backend project
- Frontend project
- TypeScript configuration
- Environment variables
- Development scripts
- Code formatting
- ESLint

## 0.4 Backend Architecture
- Layered architecture
- Module boundaries
- Request lifecycle
- Controller
- Service
- Repository / data-access layer
- Middleware
- Error handling
- Configuration
- Dependency direction

## 0.5 Initial Project Structure
- backend/
- frontend/
- notes/
- documentation

---

# Phase 1 - Node.js, HTTP & Express Fundamentals

## 1.1 Node.js Fundamentals
- Runtime model
- Event loop
- Non-blocking I/O
- Async programming
- Promises
- async/await
- Error propagation

## 1.2 HTTP Fundamentals
- Request / response
- HTTP methods
- Headers
- Status codes
- Query parameters
- Path parameters
- Request body
- Cookies
- Content types

## 1.3 Express
- Application setup
- Routing
- Middleware
- Request lifecycle
- Error middleware
- Router organization

## 1.4 API Design
- REST principles
- Resource naming
- HTTP status codes
- Request validation
- Response structure
- Error response design

---

# Phase 2 - CRUD & Database Interaction

## 2.1 Basic CRUD
- Create
- Read
- Update
- Delete

## 2.2 CRUD With Different Requirements
- Single document
- Multiple documents
- Partial updates
- Full replacement
- Conditional updates
- Bulk operations

## 2.3 Database Interaction
- Query selection
- Projection
- Sorting
- Filtering
- Population
- Aggregation

## 2.4 Efficient Database Operations
- Avoid unnecessary queries
- Avoid over-fetching
- Projection
- Batch operations
- Bulk writes
- Query optimization

## 2.5 API-Level Concerns
- Validation
- Error handling
- Consistent responses
- Edge cases

---

# Phase 3 - MongoDB & Mongoose Fundamentals

## 3.1 MongoDB Fundamentals
- Documents
- Collections
- BSON
- ObjectId
- MongoDB architecture

## 3.2 Mongoose
- Schema
- Model
- Document
- Schema validation
- Middleware
- Hooks
- Virtuals
- Instance/static methods

## 3.3 MongoDB Data Modeling
- Embedding
- Referencing
- One-to-one
- One-to-many
- Many-to-many
- Cardinality
- Read vs write patterns
- Denormalization

## 3.4 Booking Data Model
- User
- Property
- Availability
- Booking
- Review

## 3.5 Schema Evolution
- Adding fields
- Changing fields
- Backward compatibility
- Data migration concepts

---

# Phase 4 - MongoDB CRUD Deep Dive

## 4.1 Create Operations
- insertOne
- insertMany
- Mongoose create
- Bulk insert

## 4.2 Read Operations
- find
- findOne
- projections
- sorting
- filtering
- population

## 4.3 Update Operations
- $set
- $inc
- $push
- $pull
- $addToSet
- Conditional updates
- Atomic updates

## 4.4 Delete Operations
- deleteOne
- deleteMany
- Soft delete
- Hard delete

## 4.5 Bulk Operations
- bulkWrite
- Ordered vs unordered operations

## 4.6 Aggregation
- $match
- $project
- $group
- $sort
- $lookup
- $unwind
- $facet
- Aggregation pipelines

---

# Phase 5 - MongoDB Indexing & Query Optimization

## 5.1 Why Indexes Exist
- Collection scans
- Index scans
- Query performance

## 5.2 Index Types
- Single-field
- Compound
- Multikey
- Unique
- Partial
- Sparse
- Text

## 5.3 Compound Index Design
- Equality
- Sort
- Range
- ESR principle

## 5.4 Query Analysis
- explain()
- Query planner
- Winning plan
- Keys examined
- Documents examined

## 5.5 Query Optimization
- Projection
- Index selection
- Avoiding unnecessary queries
- Query shape

## 5.6 Pagination
- Offset pagination
- Cursor pagination
- Performance comparison
- Stable pagination

## 5.7 Large Dataset Problems
- Large collections
- High-cardinality fields
- Index overhead
- Write performance trade-offs

---

# Phase 6 - Testing & End-to-End Testing

## 6.1 Testing Fundamentals
- Why testing matters
- Test pyramid
- Unit vs integration vs E2E

## 6.2 Unit Testing
- Pure functions
- Services
- Business logic
- Mocking

## 6.3 Integration Testing
- Database interaction
- Repository/data-access testing
- Test database

## 6.4 API Testing
- HTTP requests
- Status codes
- Response validation
- Error scenarios

## 6.5 Test Infrastructure
- Test environment
- Test database
- Fixtures
- Factories
- Seed data
- Test cleanup
- Isolation

## 6.6 End-to-End Testing
- Complete user workflows
- Frontend -> Backend -> Database
- Authentication flow
- Booking flow

## 6.7 Advanced Testing
- Failure testing
- Race-condition testing
- Concurrent requests
- Retry testing

---

# Phase 7 - Authentication & Security

## 7.1 Authentication
- Registration
- Login
- Password hashing
- Sessions / tokens
- JWT concepts

## 7.2 Authorization
- Authentication vs authorization
- Role-based access
- Resource ownership

## 7.3 Security
- Input validation
- Password security
- CORS
- Rate limiting
- Security headers
- Secrets management

## 7.4 Authentication Testing
- Invalid credentials
- Expired credentials
- Unauthorized access
- Role restrictions

---

# Phase 8 - MongoDB Transactions & Atomicity

## 8.1 Atomic Operations
- Single-document atomicity
- Atomic update patterns

## 8.2 Transactions
- Sessions
- Transaction lifecycle
- Commit
- Abort

## 8.3 Transaction Design
- When transactions are required
- When transactions are unnecessary
- Transaction boundaries

## 8.4 Booking Transactions
- Booking creation
- Availability update
- Rollback scenarios

## 8.5 Transaction Failure
- Exceptions
- Retries
- Partial failures
- Transaction conflicts

---

# Phase 9 - Concurrency & Race Conditions

## 9.1 Concurrency Fundamentals
- Concurrent requests
- Race conditions
- Lost updates
- Check-then-act problem

## 9.2 Double Booking Problem
- Concurrent booking requests
- Why naive implementations fail

## 9.3 Atomic Solutions
- Atomic conditional updates
- Unique constraints
- Database-level guarantees

## 9.4 Optimistic Concurrency
- Versioning
- Conflict detection
- Retry

## 9.5 Pessimistic Approaches
- Locking concepts
- When locking is useful

## 9.6 Concurrent Testing
- Simultaneous requests
- Stress scenarios
- Reproducing race conditions

---

# Phase 10 - Redis Fundamentals

## 10.1 Redis Fundamentals
- In-memory storage
- Key/value model
- TTL
- Data structures

## 10.2 Redis Use Cases
- Cache
- Session storage
- Rate limiting
- Distributed coordination

## 10.3 Redis Integration
- Node.js client
- Connection management
- Error handling

---

# Phase 11 - Caching & Performance

## 11.1 Caching Fundamentals
- Why caching exists
- Cache-aside
- Read-through concepts
- Write strategies

## 11.2 Cache Design
- Cache keys
- TTL
- Invalidation
- Stale data

## 11.3 Booking Application Caching
- Property/listing data
- Availability reads
- Search results

## 11.4 Cache Failure
- Redis unavailable
- Cache miss
- Stampede

## 11.5 Cache Performance
- Hit rate
- Database load
- Latency comparison

---

# Phase 12 - Distributed Locking

## 12.1 Why Locks Are Needed
- Multi-instance applications
- Shared resources
- Race conditions

## 12.2 Redis Distributed Lock
- Lock acquisition
- Lock expiration
- Lock release

## 12.3 Lock Safety
- Ownership
- Expiry
- Failure scenarios
- Lock contention

## 12.4 Booking Use Cases
- Reservation processing
- Resource protection

## 12.5 Lock Limitations
- When not to use distributed locks
- Database atomicity vs distributed locking

---

# Phase 13 - Idempotency

## 13.1 Idempotency Fundamentals
- Idempotent vs non-idempotent operations
- Duplicate requests

## 13.2 Idempotency Keys
- Client-generated keys
- Server storage
- Request lifecycle

## 13.3 Booking Idempotency
- Duplicate booking requests
- Retry behavior

## 13.4 Failure Scenarios
- Request timeout
- Server succeeds but client doesn't receive response
- Client retries

## 13.5 Idempotency Testing
- Duplicate requests
- Concurrent duplicate requests
- Partial failures

---

# Phase 14 - Queues & Workers

## 14.1 Why Queues
- Synchronous vs asynchronous work
- Long-running operations

## 14.2 Queue Fundamentals
- Producer
- Queue
- Consumer
- Worker

## 14.3 Booking Use Cases
- Email
- Notifications
- Background processing

## 14.4 Worker Design
- Job processing
- Job status
- Concurrency

## 14.5 Failure Handling
- Retry
- Backoff
- Failed jobs
- Dead-letter concepts

---

# Phase 15 - Reliability Engineering

## 15.1 Timeouts
- Request timeout
- Database timeout
- External service timeout

## 15.2 Retries
- When to retry
- Retry limits
- Exponential backoff
- Jitter

## 15.3 Failure Handling
- Partial failure
- Dependency failure
- Database failure
- Redis failure

## 15.4 Reliability Patterns
- Circuit breaker concepts
- Bulkheads
- Graceful degradation

## 15.5 Idempotent Workers
- Duplicate jobs
- Safe retries

---

# Phase 16 - MongoDB Replication & Scaling

## 16.1 Replica Sets
- Primary
- Secondary
- Election

## 16.2 Replication
- Oplog
- Replication lag
- Failover

## 16.3 Read Preference
- Primary
- Secondary
- Secondary preferred

## 16.4 Write Concern
- Acknowledgment
- Majority writes

## 16.5 Read Concern
- Consistency levels

## 16.6 Horizontal Scaling
- Sharding concepts
- Shard key
- Distribution

## 16.7 Scaling Trade-offs
- Read scaling
- Write scaling
- Consistency
- Operational complexity

---

# Phase 17 - MongoDB Change Streams & CDC

## 17.1 Change Streams
- What they are
- Events
- Watching collections

## 17.2 Event Types
- Insert
- Update
- Delete
- Replace

## 17.3 Change Stream Consumer
- Event processing
- Resume tokens
- Restart behavior

## 17.4 CDC
- Change Data Capture concepts
- MongoDB as event source

## 17.5 Failure Handling
- Consumer crashes
- Duplicate events
- Resume behavior

---

# Phase 18 - Event-Driven Architecture

## 18.1 Events
- Domain events
- Event producers
- Event consumers

## 18.2 Event Design
- Event schema
- Event versioning
- Event naming

## 18.3 Asynchronous Architecture
- Producers
- Consumers
- Queues
- Event processing

## 18.4 Booking Events
- BookingCreated
- BookingCancelled
- PaymentCompleted
- etc.

## 18.5 Event Reliability
- Duplicate events
- Ordering
- Retries
- Dead-letter handling

---

# Phase 19 - Transactional Outbox

## 19.1 Dual Write Problem
- Database write
- Event publishing
- Failure between the two

## 19.2 Outbox Pattern
- Transactional write
- Outbox record
- Event publisher

## 19.3 Outbox Processor
- Polling / Change Streams
- Publishing
- Marking processed

## 19.4 Failure Handling
- Duplicate publishing
- Consumer idempotency
- Publisher crash

---

# Phase 20 - CQRS

## 20.1 CQRS Fundamentals
- Commands
- Queries
- Write model
- Read model

## 20.2 Why CQRS
- Read-heavy systems
- Different read/write requirements

## 20.3 MongoDB CQRS
- Separate write/read collections
- Read projections

## 20.4 Building Read Models
- Events
- Projection
- Rebuilding projections

## 20.5 CQRS Trade-offs
- Complexity
- Eventual consistency
- When NOT to use CQRS

---

# Phase 21 - Eventual Consistency

## 21.1 Strong vs Eventual Consistency

## 21.2 Why Eventual Consistency Exists

## 21.3 Stale Reads

## 21.4 User Experience
- Pending states
- Processing states
- Retry states

## 21.5 Reconciliation
- Detecting inconsistencies
- Repair processes

---

# Phase 22 - Saga & Distributed Workflows

## 22.1 Distributed Transactions Problem

## 22.2 Saga Fundamentals
- Steps
- State
- Compensation

## 22.3 Booking Workflow
- Booking
- Payment
- Availability
- Notification

## 22.4 Failure Scenarios
- Payment failure
- Availability failure
- Notification failure

## 22.5 Compensation
- Rollback-like behavior
- Compensating actions

## 22.6 Orchestration vs Choreography

---

# Phase 23 - Microservices

## 23.1 Monolith First
- Modular monolith
- Module boundaries

## 23.2 Identifying Service Boundaries
- Booking
- Payment
- Notification

## 23.3 Service Communication
- HTTP
- Events
- Queues

## 23.4 Data Ownership
- Database per service concept
- No shared database assumptions

## 23.5 Distributed Failure
- Network failures
- Timeouts
- Retries

## 23.6 When NOT to Use Microservices

---

# Phase 24 - Backend System Design

## 24.1 Requirement Analysis
- Functional requirements
- Non-functional requirements

## 24.2 Capacity Estimation
- Users
- Requests
- Storage
- Traffic

## 24.3 API Design

## 24.4 Database Design

## 24.5 Caching

## 24.6 Queues

## 24.7 Scaling

## 24.8 Consistency

## 24.9 Availability

## 24.10 Failure Handling

## 24.11 Trade-offs

---

# Phase 25 - Production Engineering

## 25.1 Logging
- Structured logs
- Log levels
- Correlation IDs

## 25.2 Monitoring
- Metrics
- Latency
- Error rate
- Throughput

## 25.3 Observability
- Logs
- Metrics
- Traces

## 25.4 Deployment
- Frontend deployment
- Backend deployment
- Environment configuration

## 25.5 CI/CD
- Tests
- Build
- Deployment pipeline

## 25.6 Production Security
- Secrets
- Access control
- Dependency security

## 25.7 Performance
- Load testing
- Bottleneck identification
- Profiling

## 25.8 Operational Readiness
- Health checks
- Graceful shutdown
- Database migrations
- Backup / recovery concepts

---

# Completion Criteria

A phase is not considered complete simply because the code works.

A phase should normally include:

- [ ] Concept understood
- [ ] Problem understood
- [ ] Pseudocode written
- [ ] Implementation written manually
- [ ] Tests written manually
- [ ] Edge cases considered
- [ ] Failure cases tested where relevant
- [ ] Optimization considered
- [ ] Trade-offs understood
- [ ] Notes completed

---

# Learning Principle

Advanced technology should only be introduced when the application encounters a problem that justifies it.

Do not introduce:
- Redis
- Distributed locks
- Queues
- CDC
- CQRS
- Saga
- Microservices

simply because they are on the roadmap.

First encounter the problem.

Then understand the limitations of the simpler solution.

Then introduce the pattern as a solution.

---

# Status Legend

- [ ] Not Started
- [~] In Progress
- [x] Completed
- [!] Needs Review