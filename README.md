# JPMorgan - Financial Transaction Processing System

A production-grade financial transaction processing system built with Spring Boot and Apache Kafka, developed as part of the JPMorgan Chase Software Engineering Virtual Experience Program.

## 🚀 Features

- **Real-time Transaction Processing** - Asynchronous message processing via Apache Kafka
- **External API Integration** - Service-to-service communication using RestTemplate
- **ACID-Compliant Database** - JPA/Hibernate with H2 in-memory database
- **Transaction Validation** - Three-tier validation (user verification, balance checks)
- **RESTful API** - Balance query endpoint for user account information
- **Incentive System** - Automatic bonus calculation through external microservice

## 🛠️ Tech Stack

**Backend Framework:**
- Spring Boot 3.2.5
- Java 17

**Message Queue:**
- Apache Kafka (Embedded for testing)

**Database:**
- H2 (in-memory)
- JPA/Hibernate ORM
- Spring Data JPA

**API Communication:**
- RestTemplate (HTTP client)
- JSON serialization/deserialization

**Build & Testing:**
- Maven
- JUnit 5
- Spring Boot Test

## 📐 Architecture
```
┌─────────────────────────────────────────────────────┐
│              MIDAS CORE SERVICE                     │
│                                                     │
│  REST API (Port 33400)                             │
│  GET /balance?userId=X                             │
│         ↓                                          │
│  ┌──────────────────────┐                         │
│  │ BalanceController    │                         │
│  └──────────┬───────────┘                         │
│             ↓                                      │
│  ┌──────────────────────┐      ┌───────────────┐  │
│  │ TransactionService   │ ←──→ │ UserRepository│  │
│  │  - Validate          │      └───────────────┘  │
│  │  - Get Incentive     │                         │
│  │  - Update Balances   │      ┌───────────────┐  │
│  └──────────┬───────────┘      │IncentiveService│  │
│             ↓                  └───────┬───────┘  │
│  ┌──────────────────────┐             ↓          │
│  │ TransactionConsumer  │      External API      │
│  │  (Kafka Listener)    │    (localhost:8081)    │
│  └──────────┬───────────┘                         │
│             ↓                                      │
│      Apache Kafka                                  │
│   (trader-updates topic)                          │
│             ↓                                      │
│  ┌──────────────────────┐                         │
│  │   H2 Database        │                         │
│  │  - user_record       │                         │
│  │  - transaction_record│                         │
│  └──────────────────────┘                         │
└─────────────────────────────────────────────────────┘
```

## 💡 Key Concepts Demonstrated

### Transaction Processing Flow
1. Transaction received via Kafka topic
2. Validate sender exists
3. Validate recipient exists  
4. Validate sufficient balance
5. Call external Incentive API for bonus calculation
6. Update sender balance (deduct transaction amount)
7. Update recipient balance (add transaction amount + incentive)
8. Persist transaction record to database

### Architectural Decisions
- **Monolithic Read/Write Integration**: Combined Kafka consumer (write path) and REST API (read path) in single service for rapid development
- **Pragmatic over Pure**: Chose deployment efficiency over strict microservice separation for simple use cases
- **Reversible Design**: Structured code to allow easy extraction into separate services if complexity grows

## 🔧 Setup & Installation

### Prerequisites
- Java 17 or higher
- Maven 3.6+

### Running the Application

1. **Clone the repository:**
```bash
git clone https://github.com/YOUR_USERNAME/midas-core-transaction-system.git
cd midas-core-transaction-system
```

2. **Build the project:**
```bash
mvn clean install
```

3. **Start the Incentive API** (in Terminal 1):
```bash
java -jar services/transaction-incentive-api.jar
```

4. **Run the application** (in Terminal 2):
```bash
mvn spring-boot:run
```

The application will start on port **33400**.

### Running Tests

**Run all tests:**
```bash
mvn test
```

**Run specific task tests:**
```bash
mvn test -Dtest=TaskTwoTests
mvn test -Dtest=TaskThreeTests
mvn test -Dtest=TaskFourTests
mvn test -Dtest=TaskFiveTests
```

## 📡 API Endpoints

### Get User Balance
```http
GET /balance?userId={userId}
```

**Parameters:**
- `userId` (required): User ID to query

**Response:**
```json
{
  "amount": 1326.98
}
```

**Example:**
```bash
curl "http://localhost:33400/balance?userId=1"
```

## 🗄️ Database Schema

### UserRecord
| Column  | Type  | Description        |
|---------|-------|--------------------|
| id      | BIGINT| Primary key        |
| name    | VARCHAR| User name         |
| balance | FLOAT | Account balance    |

### TransactionRecord
| Column          | Type      | Description                    |
|-----------------|-----------|--------------------------------|
| id              | BIGINT    | Primary key                    |
| sender_id       | BIGINT    | Foreign key to UserRecord      |
| recipient_id    | BIGINT    | Foreign key to UserRecord      |
| amount          | FLOAT     | Transaction amount             |
| incentive_amount| FLOAT     | Bonus amount from Incentive API|
| timestamp       | TIMESTAMP | Transaction time               |

## 📚 What I Learned

### Technical Skills
- Building microservices with Spring Boot
- Event-driven architecture with Apache Kafka
- External API integration using RestTemplate
- JPA/Hibernate ORM and entity relationships
- RESTful API design and implementation
- ACID transaction management
- Maven build automation

### Software Engineering Principles
- Balancing architectural purity with pragmatic delivery
- Making reversible vs. irreversible design decisions
- API contracts as team boundaries
- Service-to-service communication patterns
- YAGNI (You Aren't Gonna Need It) principle

## 📝 Project Tasks Completed

- ✅ **Task 1**: Environment setup and configuration
- ✅ **Task 2**: Kafka consumer implementation for transaction processing
- ✅ **Task 3**: Database integration with validation and persistence
- ✅ **Task 4**: External Incentive API integration
- ✅ **Task 5**: REST API endpoint for balance queries

## 🎯 Future Enhancements

- [ ] Separate read/write services (CQRS pattern)
- [ ] Add authentication/authorization (Spring Security + JWT)
- [ ] Implement transaction history endpoint
- [ ] Add PostgreSQL for production use
- [ ] Deploy to AWS/Azure with containerization (Docker)
- [ ] Add monitoring with Prometheus/Grafana
- [ ] Implement idempotency for duplicate transaction handling

## 📄 License

This project was created as part of the JPMorgan Chase Software Engineering Virtual Experience Program.

## 🤝 Acknowledgments

- JPMorgan Chase for the virtual experience program
- Forage for hosting the program

---

**Built with ☕ and Spring Boot**


