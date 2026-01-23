# Invoice Management API

A production-ready REST API for invoice management built with Spring Boot, designed for freelancers and small-to-medium businesses.

![Java](https://img.shields.io/badge/Java-17-orange?style=flat&logo=java)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.2-green?style=flat&logo=springboot)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15-blue?style=flat&logo=postgresql)
![License](https://img.shields.io/badge/License-MIT-yellow?style=flat)

## 🎯 Features

- **Invoice Management**: Create, update, delete, and track invoices
- **Customer Management**: Manage customer information and history
- **Service Catalog**: Predefined services for quick invoice creation
- **Payment Tracking**: Monitor payment status and overdue invoices
- **PDF Export**: Generate professional, printable invoices
- **JWT Authentication**: Secure API access with role-based authorization
- **Comprehensive Reporting**: Monthly revenue, outstanding payments, customer analytics
- **Audit Trail**: Complete logging of all changes
- **RESTful Design**: Clean, well-documented API endpoints

## 🛠️ Tech Stack

- **Backend**: Java 17, Spring Boot 3.2
- **Security**: Spring Security, JWT
- **Database**: PostgreSQL 15+
- **ORM**: Spring Data JPA / Hibernate
- **Build Tool**: Maven
- **Testing**: JUnit 5, Mockito
- **Documentation**: Swagger/OpenAPI (planned)

## 📋 Prerequisites

- Java 17 or higher
- Maven 3.6+
- PostgreSQL 15+
- Git

## 🚀 Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/YOUR-USERNAME/invoice-management-api.git
cd invoice-management-api
```

### 2. Set Up Database

```sql
-- Connect to PostgreSQL
psql -U postgres

-- Create database and user
CREATE DATABASE invoice_db;
CREATE USER invoice_user WITH PASSWORD 'invoice2024';
GRANT ALL PRIVILEGES ON DATABASE invoice_db TO invoice_user;

-- For PostgreSQL 15+
\c invoice_db
GRANT ALL ON SCHEMA public TO invoice_user;
```

### 3. Configure Application

Edit `src/main/resources/application.properties`:

```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/invoice_db
spring.datasource.username=invoice_user
spring.datasource.password=invoice2024
```

### 4. Run the Application

```bash
# Using Maven wrapper
./mvnw spring-boot:run

# Or if Maven is installed globally
mvn spring-boot:run
```

The API will start on `http://localhost:8080`

### 5. Test the API

```bash
# Health check
curl http://localhost:8080/api/health
```

## 📚 API Endpoints

### Authentication

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| POST | `/api/auth/register` | Register new user | No |
| POST | `/api/auth/login` | Login (returns JWT token) | No |
| POST | `/api/auth/refresh` | Refresh JWT token | Yes |

### Customers

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| GET | `/api/customers` | Get all customers | Yes |
| GET | `/api/customers/{id}` | Get customer by ID | Yes |
| POST | `/api/customers` | Create new customer | Yes |
| PUT | `/api/customers/{id}` | Update customer | Yes |
| DELETE | `/api/customers/{id}` | Delete customer | Yes |
| GET | `/api/customers/{id}/invoices` | Get customer's invoice history | Yes |

### Invoices

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| GET | `/api/invoices` | Get all invoices | Yes |
| GET | `/api/invoices/{id}` | Get invoice by ID | Yes |
| POST | `/api/invoices` | Create new invoice | Yes |
| PUT | `/api/invoices/{id}` | Update invoice | Yes |
| DELETE | `/api/invoices/{id}` | Delete invoice (soft delete) | Yes |
| PATCH | `/api/invoices/{id}/status` | Update invoice status | Yes |
| GET | `/api/invoices/{id}/pdf` | Download invoice as PDF | Yes |
| GET | `/api/invoices/overdue` | Get overdue invoices | Yes |

### Invoice Items

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| POST | `/api/invoices/{id}/items` | Add item to invoice | Yes |
| PUT | `/api/invoices/{id}/items/{itemId}` | Update invoice item | Yes |
| DELETE | `/api/invoices/{id}/items/{itemId}` | Remove item from invoice | Yes |

### Services

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| GET | `/api/services` | Get all services | Yes |
| GET | `/api/services/{id}` | Get service by ID | Yes |
| POST | `/api/services` | Create new service | Yes |
| PUT | `/api/services/{id}` | Update service | Yes |
| DELETE | `/api/services/{id}` | Delete service | Yes |

### Payments

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| GET | `/api/invoices/{id}/payments` | Get all payments for invoice | Yes |
| POST | `/api/invoices/{id}/payments` | Record payment | Yes |
| GET | `/api/payments` | Get all payments | Yes |

### Reports

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| GET | `/api/reports/monthly` | Monthly revenue report | Yes |
| GET | `/api/reports/outstanding` | Outstanding invoices report | Yes |
| GET | `/api/reports/customer/{id}` | Customer-specific report | Yes |
| GET | `/api/reports/export/csv` | Export data as CSV | Yes |

### Admin (Role: ADMIN only)

| Method | Endpoint | Description | Auth Required |
|--------|----------|-------------|---------------|
| GET | `/api/admin/users` | Get all users | Yes (Admin) |
| PUT | `/api/admin/users/{id}/role` | Update user role | Yes (Admin) |
| GET | `/api/admin/audit-logs` | Get audit trail | Yes (Admin) |

## 🔐 Authentication

The API uses JWT (JSON Web Token) for authentication.

### Login Flow

1. **Register** or **Login** to get JWT token:

```bash
curl -X POST http://localhost:8080/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "username": "user@example.com",
    "password": "password123"
  }'
```

2. **Response**:

```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "type": "Bearer",
  "expiresIn": 86400
}
```

3. **Use token** in subsequent requests:

```bash
curl -X GET http://localhost:8080/api/invoices \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

## 📊 Database Schema

```
users
├── id (PK)
├── username
├── email
├── password_hash
├── role (ADMIN, USER)
└── created_at

customers
├── id (PK)
├── name
├── email
├── address
├── tax_id
├── phone
└── created_at

invoices
├── id (PK)
├── invoice_number
├── customer_id (FK)
├── issue_date
├── due_date
├── total_amount
├── status (DRAFT, SENT, PAID, OVERDUE)
├── notes
└── created_at

invoice_items
├── id (PK)
├── invoice_id (FK)
├── description
├── quantity
├── unit_price
└── total

services
├── id (PK)
├── name
├── description
├── price
└── user_id (FK)

payments
├── id (PK)
├── invoice_id (FK)
├── amount_paid
├── payment_date
└── payment_method

audit_log
├── id (PK)
├── entity_type
├── entity_id
├── action
├── changed_by (FK)
├── timestamp
└── changes (JSON)
```

## 🧪 Testing

```bash
# Run all tests
./mvnw test

# Run with coverage
./mvnw test jacoco:report

# Run integration tests only
./mvnw verify -P integration-tests
```

## 📦 Building for Production

```bash
# Create JAR file
./mvnw clean package

# Run JAR
java -jar target/invoice-api-1.0.0.jar
```

## 🌍 Environment Variables

For production, use environment variables instead of `application.properties`:

```bash
export DB_URL=jdbc:postgresql://your-db-host:5432/invoice_db
export DB_USERNAME=your_db_user
export DB_PASSWORD=your_db_password
export JWT_SECRET=your_secret_key_here
export JWT_EXPIRATION=86400000
```

## 📝 API Documentation

Once running, access Swagger UI at:
```
http://localhost:8080/swagger-ui.html
```

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.


## 🙏 Acknowledgments

- Spring Boot Team for the excellent framework
- PostgreSQL Community
- All contributors and supporters

---

**Note**: This is a portfolio project demonstrating REST API development, security, and database design skills.