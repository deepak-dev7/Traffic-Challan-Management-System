# FineTrack — Traffic Challan Management System
> **Tagline:** *Traffic fines. Managed transparently.*  
> **Platform:** Modern Traffic Challan Management Platform

---

FineTrack is an enterprise-grade traffic citation management platform engineered to streamline fine issuance, vehicle violation tracking, penalty administration, and notification dispatch. FineTrack features a modular monolithic primary service built on **Spring Boot 3.3**, **Thymeleaf**, and **Spring Data JPA/Hibernate 6** with an independent **Notification Microservice** running on Port 8081.

---

## 🚀 Key Highlights & Architectural Overview

FineTrack is built with a **Modular Monolith + Independent Notification Microservice** architecture:

| Application Module | Service Port | Technology Stack | Description |
| :--- | :--- | :--- | :--- |
| **`finetrack-main`** | `8080` | Spring Boot 3.3.4, Thymeleaf, Spring Security 6, Spring Data JPA, Hibernate 6, MySQL, Bootstrap 5 | Core Web Portal & REST API Server |
| **`finetrack-notification-service`** | `8081` | Spring Boot 3.3.4, REST Web Services, Jakarta Validation | Standalone Notification Microservice for citizen alert dispatch |

---

## 🔐 Credentials & Portals Quick Reference

The application features **two strictly separated portals**:

### 1. Vehicle Owner Portal (`ROLE_USER`)
- **Login URL:** [http://localhost:8080/login](http://localhost:8080/login)
- **Registration URL:** [http://localhost:8080/register](http://localhost:8080/register)
- **Standard Test Account 1:** `rajesh` / `password123` (Vehicle: `TN-09-AB-1234`)
- **Standard Test Account 2:** `priya` / `password123` (Vehicle: `TN-07-CD-5678`)
- **Standard Test Account 3:** `anand` / `password123` (Vehicle: `TN-02-XY-9012`)
- **Capabilities:** View registered vehicle, monitor personal challan history, filter by Pending/Paid status, live search by citation ID or location, inspect printable e-challan notice. *Strictly zero-trust isolated to own vehicle.*

### 2. Administrator Portal (`ROLE_ADMIN`)
- **Login URL:** [http://localhost:8080/admin/login](http://localhost:8080/admin/login)
- **Initial Administrator:** `admin` / `Admin@123` *(Auto-bootstrapped on startup; no public admin signup)*
- **Capabilities:** Monitor database-driven aggregated metrics, issue new challans with category defaults, search and filter across vehicles, edit challans, toggle payment status (Paid/Pending), delete with confirmation, inspect vehicle registry, and inspect real-time Spring AOP audit logs.

---

## 🛠️ Technology Stack

- **Backend:** Java 21, Spring Boot 3.3.4 (MVC, Data JPA, Security, AOP, RestClient)
- **ORM / Persistence:** Hibernate 6.5 with MySQL 8.0 (and in-memory H2 for test isolation)
- **Frontend:** Thymeleaf 3.1, HTML5, CSS3, Bootstrap 5.3.3, Bootstrap Icons, JavaScript
- **Microservices:** Independent Spring Boot REST service on Port 8081
- **Security:** Spring Security 6 session-based auth, BCrypt password hashing, Cookie CSRF tokens, strict RBAC
- **Build & Quality:** Apache Maven 3.9+, JUnit 5, Mockito, MockMvc (15 automated unit & integration tests)

---

## 📦 Project Structure

```
c:\Users\deepu\OneDrive\Documents\fine
├── pom.xml                                  # Root Maven Aggregator POM
├── run-all.bat                              # One-click Windows launcher for both services
├── run-main.bat                             # Launch Main Web Application (Port 8080)
├── run-notification.bat                     # Launch Notification Microservice (Port 8081)
├── finetrack-main/                          # Core Web Application (Port 8080)
│   ├── pom.xml
│   └── src/
│       ├── main/
│       │   ├── java/com/finetrack/
│       │   │   ├── FineTrackApplication.java
│       │   │   ├── config/                  # SecurityConfig, DataInitializer
│       │   │   ├── controller/              # HomeController, UserDashboard, Admin, Demo
│       │   │   ├── rest/                    # ChallanRestController, GlobalRestExceptionHandler
│       │   │   ├── dto/                     # Registration, Form, Response DTOs
│       │   │   ├── entity/                  # VehicleUser, Admin, Challan, AuditLog
│       │   │   │   └── demo/                # Person, Officer, AdminPerson (Inheritance)
│       │   │   ├── repository/              # Spring Data JPA Repositories & HQL queries
│       │   │   ├── service/                 # ChallanService, VehicleUserService, RestClient
│       │   │   │   └── demo/                # HibernateSessionDemo, SpringContextDemo
│       │   │   ├── security/                # CustomUserDetailsService, SuccessHandler
│       │   │   └── aspect/                  # ChallanAuditAspect (@Aspect cross-cutting logger)
│       │   └── resources/
│       │       ├── application.properties   # MySQL database and server config
│       │       ├── static/css/style.css     # Premium design system CSS
│       │       ├── static/js/main.js        # Password toggle, fine calculation, modals
│       │       └── templates/               # Thymeleaf templates (responsive fintech design)
│       └── test/                            # Comprehensive JUnit 5 & MockMvc tests
├── finetrack-notification-service/          # Independent Microservice (Port 8081)
│   ├── pom.xml
│   └── src/
│       ├── main/java/com/finetrack/notification/
│       │   ├── NotificationServiceApplication.java
│       │   ├── controller/NotificationRestController.java
│       │   ├── service/NotificationDispatcherService.java
│       │   └── dto/
│       └── test/                            # Microservice REST validation tests
└── docs/
    ├── System_Architecture_and_Engineering_Report.md  # Comprehensive platform engineering specifications
    ├── FineTrack_Postman_Collection.json             # Ready-to-import Postman REST collection
    └── architecture_diagrams.md                      # Mermaid diagrams (ER, System, Sequence)
```

---

## ⚡ Quick Start Guide

### Step 1: Verify Prerequisites
Ensure Java 21 and Maven are installed:
```powershell
java -version
mvn -version
```

Ensure MySQL is running on `localhost:3306`. The application automatically creates the database `finetrack_db` if it does not already exist.

### Step 2: Run the Complete Automated Test Suite
From the root directory, execute:
```powershell
mvn clean test
```
*Expected Result:* **BUILD SUCCESS** across all modules with all 15 unit and integration tests passing.

### Step 3: Launch Both Applications

#### Option A: One-Click Launch (Windows)
Run `run-all.bat` in PowerShell or Command Prompt:
```powershell
.\run-all.bat
```

#### Option B: Terminal Commands
In Terminal 1 (Notification Microservice):
```powershell
cd finetrack-notification-service
mvn spring-boot:run
```

In Terminal 2 (FineTrack Web Application):
```powershell
cd finetrack-main
mvn spring-boot:run
```

---

## 🎯 Architecture & Advanced Technical Capabilities

1. **Optimized HQL Query Engine:**
   - Parameterized entity query to isolate challans by vehicle registration:
     ```sql
     SELECT c FROM Challan c WHERE UPPER(c.vehicleUser.vehicleNumber) = UPPER(:vehicleNumber) ORDER BY c.violationDate DESC
     ```
   - Parameterized entity query filtering pending challans above a specified threshold:
     ```sql
     SELECT c FROM Challan c WHERE c.paymentStatus = :status AND c.fineAmount >= :minAmount ORDER BY c.fineAmount DESC
     ```
2. **Hibernate `SINGLE_TABLE` Inheritance Mapping:** Polymorphic retrieval of base entity `Person` and subclasses `Officer` (`person_type='OFFICER'`) and `AdminPerson` (`person_type='ADMIN_PERSON'`).
3. **Programmatic `SessionFactory`, `Session`, and `Transaction` Management:** Demonstrates explicit unwrapping of `EntityManagerFactory`, opening a low-level session, initiating a transaction, persisting an entity, committing, and closing resources.
4. **Spring IoC & Dependency Injection:** Strict constructor-based injection with immutability guarantees.
5. **Spring AOP Audit Logging:** Intercepts `createChallan`, `updateChallan`, `updatePaymentStatus`, and `deleteChallan` methods using `@Aspect` and `@AfterReturning`, logging structured audit records without capturing sensitive credentials.

---

## 🌐 RESTful API Integration

Import the Postman collection located at:
`docs/FineTrack_Postman_Collection.json`

### Key Endpoints

#### 1. Admin - Get All Challans
```bash
curl -u admin:Admin@123 http://localhost:8080/api/challans
```

#### 2. Admin - Create New Challan
```bash
curl -X POST -u admin:Admin@123 http://localhost:8080/api/challans \
  -H "Content-Type: application/json" \
  -d '{
    "vehicleUserId": 1,
    "violationType": "SIGNAL_VIOLATION",
    "violationDate": "2026-10-21",
    "location": "Grand Southern Trunk Junction, Guindy",
    "fineAmount": 1500.00,
    "description": "ANPR automated detection"
  }'
```

#### 3. Admin - Update Payment Status to PAID
```bash
curl -X PATCH -u admin:Admin@123 http://localhost:8080/api/challans/1/status \
  -H "Content-Type: application/json" \
  -d '{"paymentStatus": "PAID"}'
```

#### 4. Vehicle User - Get My Challans
```bash
curl -u rajesh:password123 http://localhost:8080/api/user/challans
```

#### 5. Notification Microservice Direct Dispatch (Port 8081)
```bash
curl -X POST http://localhost:8081/api/notifications \
  -H "Content-Type: application/json" \
  -d '{
    "challanNumber": "FT-2026-104921",
    "vehicleNumber": "TN-09-AB-1234",
    "recipientEmail": "rajesh@example.com",
    "recipientName": "Rajesh Kumar",
    "violationType": "Signal Violation",
    "fineAmount": 1500.00,
    "message": "Traffic notification alert: Signal violation citation recorded."
  }'
```

---

## 🛡️ Microservice Inter-Service Communication & Resilience

- Whenever a challan is created via the web interface or REST API, `NotificationClientService` executes an HTTP POST to `http://localhost:8081/api/notifications`.
- **Fault-Tolerant Resilience:** If the notification microservice is offline or experiencing network latency, the exception is caught, a structured warning is logged, and the primary challan creation transaction commits safely to MySQL.

---

## 📋 Production Verification Checklist

- [x] Premium product landing page with dark hero section, real UI mockup preview, and professional CTAs.
- [x] Vehicle owner registration and login with password visibility toggles.
- [x] Administrative login portal with server-side authentication and no public signup.
- [x] Spring Security session authentication with BCrypt hashing and Cookie CSRF tokens.
- [x] Strict vehicle owner zero-trust data isolation (cannot view other vehicles' citations).
- [x] Admin dashboard with live database-driven aggregated statistics.
- [x] Create Challan page with auto-populated category fines and unique ID generation.
- [x] Manage Challans table with search, status filtering, violation filtering, edit, and delete confirmation.
- [x] Official printable e-challan notice view.
- [x] MySQL persistence with Spring Data JPA and Hibernate 6.
- [x] Parameterized HQL/JPQL queries for vehicle isolation and fine thresholds.
- [x] Polymorphic `SINGLE_TABLE` inheritance mapping (`Person`, `Officer`, `AdminPerson`).
- [x] Explicit Hibernate `SessionFactory`, `Session`, and `Transaction` programmatic persistence.
- [x] Constructor-based dependency injection throughout.
- [x] Spring AOP audit logging aspect intercepting all state changes.
- [x] Complete RESTful API with validation, DTOs, and standard HTTP status codes.
- [x] Independent notification microservice running on Port 8081.
- [x] Resilient HTTP inter-service communication.
- [x] Automated test suite with 100% pass rate (`mvn clean test`).
- [x] Postman collection and architecture diagrams included.
