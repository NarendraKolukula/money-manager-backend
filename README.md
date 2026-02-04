# Money Manager - Spring Boot Backend

A RESTful API backend for the Money Manager personal finance application built with Spring Boot and MongoDB.

## Features

- **Transaction Management**: Create, read, update, and delete income/expense transactions
- **Account Management**: Manage multiple accounts (Cash, Bank, Credit Card)
- **Transfer Management**: Transfer money between accounts
- **Category Management**: Organize transactions by categories
- **Dashboard & Reports**: Weekly, monthly, and yearly summaries
- **Date Range Filtering**: Filter transactions between two dates
- **Division Filtering**: Filter by Office/Personal divisions
- **12-Hour Edit Window**: Transactions can only be edited within 12 hours of creation

## Tech Stack

- **Java 17**
- **Spring Boot 3.2.0**
- **Spring Data MongoDB**
- **MongoDB Atlas** (Cloud Database)
- **Lombok** (Reduce boilerplate)
- **SpringDoc OpenAPI** (API Documentation)

## Prerequisites

- Java 17 or higher
- Maven 3.6+
- MongoDB Atlas account (or local MongoDB installation)

## Configuration

### MongoDB Atlas Setup

1. Create a free MongoDB Atlas account at https://www.mongodb.com/atlas
2. Create a new cluster
3. Create a database user with read/write permissions
4. Get your connection string

### Application Configuration

Update `src/main/resources/application.properties`:

```properties
# MongoDB Atlas Configuration
spring.data.mongodb.uri=mongodb+srv://<username>:<password>@<cluster>.mongodb.net/money_manager?retryWrites=true&w=majority
spring.data.mongodb.database=money_manager

# CORS - Add your frontend URL
app.cors.allowed-origins=http://localhost:5173,http://localhost:3000
```

For local MongoDB:

```properties
spring.data.mongodb.host=localhost
spring.data.mongodb.port=27017
spring.data.mongodb.database=money_manager
```

## Running the Application

### Using Maven

```bash
# Navigate to backend directory
cd backend

# Run the application
./mvnw spring-boot:run

# Or on Windows
mvnw.cmd spring-boot:run
```

### Using JAR

```bash
# Build the JAR
./mvnw clean package

# Run the JAR
java -jar target/money-manager-api-1.0.0.jar
```

The application will start on `http://localhost:8080`

## API Documentation

Once the application is running, access the Swagger UI:

- **Swagger UI**: http://localhost:8080/swagger-ui.html
- **OpenAPI JSON**: http://localhost:8080/api-docs

## API Endpoints

### Transactions

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/transactions` | Get all transactions (with optional filters) |
| GET | `/api/transactions/{id}` | Get transaction by ID |
| POST | `/api/transactions` | Create new transaction |
| PUT | `/api/transactions/{id}` | Update transaction (within 12 hours) |
| DELETE | `/api/transactions/{id}` | Delete transaction (within 12 hours) |

**Query Parameters for GET /api/transactions:**
- `division` - Filter by division (OFFICE, PERSONAL)
- `category` - Filter by category ID
- `startDate` - Start date (ISO format)
- `endDate` - End date (ISO format)

### Accounts

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/accounts` | Get all accounts |
| GET | `/api/accounts/{id}` | Get account by ID |
| GET | `/api/accounts/total-balance` | Get total balance |
| POST | `/api/accounts` | Create new account |
| PUT | `/api/accounts/{id}` | Update account |
| DELETE | `/api/accounts/{id}` | Delete account |

### Transfers

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/transfers` | Get all transfers |
| GET | `/api/transfers/{id}` | Get transfer by ID |
| GET | `/api/transfers/date-range` | Get transfers by date range |
| POST | `/api/transfers` | Create new transfer |
| DELETE | `/api/transfers/{id}` | Delete/reverse transfer |

### Categories

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/categories` | Get all categories |
| GET | `/api/categories/type/{type}` | Get categories by type (INCOME, EXPENSE) |
| GET | `/api/categories/{id}` | Get category by ID |
| POST | `/api/categories` | Create new category |
| PUT | `/api/categories/{id}` | Update category |
| DELETE | `/api/categories/{id}` | Delete category |

### Dashboard

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/dashboard/summary/weekly` | Get weekly summary |
| GET | `/api/dashboard/summary/monthly` | Get monthly summary |
| GET | `/api/dashboard/summary/yearly` | Get yearly summary |
| GET | `/api/dashboard/summary/custom` | Get custom date range summary |
| GET | `/api/dashboard/category-summary` | Get category breakdown |
| GET | `/api/dashboard/totals` | Get income/expense totals |

## Request/Response Examples

### Create Transaction

```bash
POST /api/transactions
Content-Type: application/json

{
  "type": "EXPENSE",
  "amount": 1500.00,
  "description": "Grocery shopping",
  "category": "food",
  "division": "PERSONAL",
  "accountId": "cash",
  "dateTime": "2024-01-15T10:30:00"
}
```

### Response

```json
{
  "success": true,
  "message": "Transaction created successfully",
  "data": {
    "id": "65a5b1234567890abcdef",
    "type": "EXPENSE",
    "amount": 1500.00,
    "description": "Grocery shopping",
    "category": "food",
    "division": "PERSONAL",
    "accountId": "cash",
    "dateTime": "2024-01-15T10:30:00",
    "createdAt": "2024-01-15T10:30:00",
    "updatedAt": "2024-01-15T10:30:00",
    "editable": true
  }
}
```

### Create Transfer

```bash
POST /api/transfers
Content-Type: application/json

{
  "fromAccountId": "bank",
  "toAccountId": "cash",
  "amount": 5000.00,
  "description": "ATM withdrawal",
  "dateTime": "2024-01-15T14:00:00"
}
```

## Project Structure

```
backend/
├── src/
│   └── main/
│       ├── java/
│       │   └── com/moneymanager/
│       │       ├── MoneyManagerApplication.java
│       │       ├── config/
│       │       │   ├── CorsConfig.java
│       │       │   ├── DataInitializer.java
│       │       │   └── OpenApiConfig.java
│       │       ├── controller/
│       │       │   ├── AccountController.java
│       │       │   ├── CategoryController.java
│       │       │   ├── DashboardController.java
│       │       │   ├── TransactionController.java
│       │       │   └── TransferController.java
│       │       ├── dto/
│       │       │   ├── AccountDTO.java
│       │       │   ├── ApiResponse.java
│       │       │   ├── CategoryDTO.java
│       │       │   ├── CategorySummaryDTO.java
│       │       │   ├── DashboardSummaryDTO.java
│       │       │   ├── FilterDTO.java
│       │       │   ├── PeriodDataDTO.java
│       │       │   ├── TransactionDTO.java
│       │       │   └── TransferDTO.java
│       │       ├── exception/
│       │       │   ├── GlobalExceptionHandler.java
│       │       │   ├── InvalidTransferException.java
│       │       │   ├── ResourceNotFoundException.java
│       │       │   └── TransactionNotEditableException.java
│       │       ├── model/
│       │       │   ├── Account.java
│       │       │   ├── Category.java
│       │       │   ├── Division.java
│       │       │   ├── Transaction.java
│       │       │   ├── TransactionType.java
│       │       │   └── Transfer.java
│       │       ├── repository/
│       │       │   ├── AccountRepository.java
│       │       │   ├── CategoryRepository.java
│       │       │   ├── TransactionRepository.java
│       │       │   └── TransferRepository.java
│       │       └── service/
│       │           ├── AccountService.java
│       │           ├── CategoryService.java
│       │           ├── DashboardService.java
│       │           ├── TransactionService.java
│       │           └── TransferService.java
│       └── resources/
│           ├── application.properties
│           └── application-dev.properties
└── pom.xml
```

## Error Handling

The API returns consistent error responses:

```json
{
  "success": false,
  "message": "Error message here",
  "data": null
}
```

### HTTP Status Codes

- `200 OK` - Successful request
- `201 Created` - Resource created successfully
- `400 Bad Request` - Validation error or invalid request
- `403 Forbidden` - Transaction cannot be edited (past 12 hours)
- `404 Not Found` - Resource not found
- `500 Internal Server Error` - Server error

## Development

### Running with Development Profile

```bash
./mvnw spring-boot:run -Dspring-boot.run.profiles=dev
```

### Building for Production

```bash
./mvnw clean package -DskipTests
```

## License

MIT License
