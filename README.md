# backend-developer-as-final-84067-jeevanprasad
Final Project Assignment - This repository contains the complete final project code and documentation.
# Resource Booking API

Secure RESTful Resource Booking System built with Java 17, Spring Boot, Spring Security, JWT, JPA and PostgreSQL.

## Requirements

- Java 17+
- Maven 3.9+
- PostgreSQL 14+ (or compatible version)

## Database

Create the database:

```sql
CREATE DATABASE resource_booking;
```

Then configure:

```bash
export DB_URL=jdbc:postgresql://localhost:5432/resource_booking
export DB_USERNAME=postgres
export DB_PASSWORD=postgres
export JWT_SECRET='replace-with-a-random-secret-at-least-32-characters-long'
```

Windows PowerShell:

```powershell
$env:DB_URL="jdbc:postgresql://localhost:5432/resource_booking"
$env:DB_USERNAME="postgres"
$env:DB_PASSWORD="postgres"
$env:JWT_SECRET="replace-with-a-random-secret-at-least-32-characters-long"
```

## Run

```bash
mvn clean test
mvn spring-boot:run
```

## Seeded accounts

Development seed data creates:

- ADMIN: `admin` / `Admin@123`
- USER: `user` / `User@123`

Change/remove these credentials before production use.

## Login

```http
POST /auth/login
Content-Type: application/json

{
  "username": "user",
  "password": "User@123"
}
```

Response:

```json
{
  "token": "...",
  "tokenType": "Bearer",
  "username": "user",
  "role": "USER"
}
```

Use:

```http
Authorization: Bearer <token>
```

## Resource API

```text
GET    /resources?page=0&size=10&sort=name,asc
GET    /resources/{id}

POST   /resources                 ADMIN
PUT    /resources/{id}            ADMIN
DELETE /resources/{id}            ADMIN
```

Create resource:

```json
{
  "name": "Meeting Room B",
  "description": "6-seat meeting room",
  "available": true
}
```

## Reservation API

USER and ADMIN can create reservations:

```http
POST /reservations
Authorization: Bearer <token>
Content-Type: application/json
```

```json
{
  "resourceId": 1,
  "price": 150.00,
  "startTime": "2026-10-01T10:00:00",
  "endTime": "2026-10-01T12:00:00"
}
```

There is intentionally **no userId field**. The reservation owner is taken from the authenticated JWT identity.

Endpoints:

```text
GET    /reservations
GET    /reservations/{id}
POST   /reservations

PUT    /reservations/{id}          ADMIN
DELETE /reservations/{id}          ADMIN
```

Filtering:

```text
GET /reservations?status=CONFIRMED&minPrice=100&maxPrice=500
```

Pagination and sorting:

```text
GET /reservations?page=0&size=10&sort=price,desc
```

Supported statuses:

```text
PENDING
CONFIRMED
CANCELLED
```

USER reservation reads are automatically restricted to that user's own reservations. ADMIN can read all reservations.

## Security

- Stateless JWT authentication
- BCrypt password hashing
- Role-based authorization
- USER cannot perform ADMIN resource CRUD
- USER cannot update/delete reservations
- USER cannot read another user's reservation
- Reservation ownership is derived from the authenticated principal
- No user-controlled `userId` in reservation creation

## Notes

This project uses `ddl-auto: update` for convenience during the assignment. For production, use Flyway/Liquibase migrations and set schema management appropriately.

The JWT secret is configured through `JWT_SECRET`; do not commit a real secret.
