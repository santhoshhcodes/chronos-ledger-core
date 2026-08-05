# 🏛️ Chronos Ledger Core

> **Enterprise B2B Multi-Tenant Engine for Biometric Attendance Tracking & Immutable Payroll Accounting**

[![Python 3.11+](https://img.shields.io/badge/python-3.11+-3776AB.svg?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.110.0-009688.svg?style=for-the-badge&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com/)
[![PostgreSQL 16](https://img.shields.io/badge/PostgreSQL-16.0-4169E1.svg?style=for-the-badge&logo=postgresql&logoColor=white)](https://www.postgresql.org/)
[![Docker](https://img.shields.io/badge/Docker-Enabled-2496ED.svg?style=for-the-badge&logo=docker&logoColor=white)](https://www.docker.com/)
[![Poetry](https://img.shields.io/badge/Poetry-Dependency_Manager-60A5FA.svg?style=for-the-badge&logo=poetry&logoColor=white)](https://python-poetry.org/)

---

# 📌 Executive Summary

**Chronos Ledger** is a high-performance, asynchronous B2B SaaS backend engineered for strict multi-tenant data isolation, high-concurrency biometric attendance ingestion, and automated transactional payroll accounting.

Unlike conventional multi-tenant applications that rely purely on application-level filtering (e.g., `WHERE tenant_id = ?`), Chronos Ledger enforces **Zero-Data-Leakage Multi-Tenancy** at the database layer using **PostgreSQL Row Level Security (RLS)** dynamically combined with FastAPI's async session lifecycle.

---

# 🎯 Problem Statement

Traditional HRMS & ERP platforms suffer from three major issues:

- **Data Leakage in Multi-Tenancy**  
  Many systems isolate tenants only through `WHERE tenant_id = ?` filters. A single coding mistake can expose one company's payroll or employee data to another.

- **Attendance Fraud & Performance Bottlenecks**  
  GPS spoofing, fake biometric timestamps, and massive morning clock-ins (8–9 AM) overload synchronous systems.

- **Mutable Payroll Records**  
  Manual payroll calculations using spreadsheets make auditing difficult and allow historical data to be modified.

Chronos Ledger solves these problems through:

- PostgreSQL Row Level Security (RLS)
- Async FastAPI architecture
- Immutable payroll ledgers
- High-throughput attendance ingestion
- Enterprise-grade multi-tenancy

---

# 🔄 End-to-End Request Flow

```text
 Field Employee Mobile App
        │
        │ HTTPS
        ▼
acme.chronos.com
        │
        ▼
Traefik / Nginx
(Subdomain Routing)
        │
        ▼
FastAPI API Gateway
        │
        ▼
JWT Authentication
        │
        ▼
Extract Tenant Context
        │
        ▼
Middleware
SET LOCAL app.current_tenant='tenant_id'
        │
        ▼
PostgreSQL 16
(Row Level Security)
        │
        ▼
Tenant-Isolated Data
        │
        ▼
Attendance Engine
        │
        ▼
Payroll Ledger
```

---

# 🏗️ System Architecture

```text
┌──────────────────────────────────────────────────────────────────────────┐
│                     CHRONOS LEDGER ARCHITECTURE                          │
├──────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  Next.js Admin Portal                                                    │
│                                                                          │
│  React Native Mobile Application                                         │
│                                                                          │
│  IoT / Biometric Attendance Devices                                      │
│                                                                          │
└───────────────┬───────────────────────────────┬──────────────────────────┘
                │                               │
                └──────── HTTPS + JWT ──────────┘
                            │
                            ▼
                 ┌───────────────────────────┐
                 │      FastAPI Engine       │
                 │ Async REST API Gateway    │
                 └─────────────┬─────────────┘
                               │
                 Authentication & Authorization
                               │
                               ▼
                 Tenant Context Middleware
                               │
     SET LOCAL app.current_tenant = '<tenant_id>'
                               │
                               ▼
              PostgreSQL 16 + Row Level Security
                               │
               ┌───────────────┴────────────────┐
               │                                │
      Shared SaaS Tables              Tenant Tables
               │                                │
               └───────────────┬────────────────┘
                               ▼
                     Business Services
                               │
                               ▼
                     Payroll Calculation
                               │
                               ▼
                    Immutable Payroll Ledger
```

---

# 🗄️ Database Architecture

## Tier 1 — Platform Shared Tables

These tables are shared across the SaaS platform.

| Table | Purpose |
|--------|---------|
| `tenants` | Organization master |
| `tenant_subscriptions` | Subscription plans |
| `saas_billing_statements` | SaaS billing ledger |

---

## Tier 2 — Tenant Protected Tables (RLS Enabled)

These tables are protected using PostgreSQL Row Level Security.

| Table | Purpose |
|--------|---------|
| `departments` | Organization hierarchy |
| `employee_identities` | Employee master & authentication |
| `site_allocations` | Office/site geofencing |
| `shift_assignments` | Shift definitions |
| `attendance_telemetry` | Clock-in & clock-out records |
| `payroll_ledgers` | Immutable payroll ledger |

---

# 🔐 Multi-Tenant Security

Every authenticated request follows this sequence:

1. User authenticates.
2. JWT is validated.
3. Tenant ID is extracted.
4. Middleware executes:

```sql
SET LOCAL app.current_tenant = 'tenant_id';
```

5. PostgreSQL automatically filters records through Row Level Security.

Example policy:

```sql
USING (
    tenant_id =
    current_setting('app.current_tenant')::uuid
)
```

No application query can accidentally access another tenant's records.

---

# ⚡ Key Features

- Enterprise Multi-Tenant SaaS
- PostgreSQL Row Level Security (RLS)
- Async FastAPI
- SQLAlchemy 2.0 Async ORM
- AsyncPG Driver
- JWT RS256 Authentication
- Docker Ready
- Poetry Dependency Management
- Redis Caching
- Sliding Window Rate Limiting
- Payroll Engine
- Attendance Engine
- ETL Data Migration Pipeline
- Immutable Payroll Ledger
- REST API
- OpenAPI / Swagger Documentation

---

# 📂 Repository Structure

```text
chronos_ledger/

├── app/
│
├── api/
│   ├── v1/
│   ├── auth.py
│   ├── employee.py
│   ├── attendance.py
│   └── payroll.py
│
├── core/
│   ├── config.py
│   ├── security.py
│   ├── database.py
│   ├── middleware.py
│   └── dependencies.py
│
├── models/
│
├── schemas/
│
├── services/
│
├── repositories/
│
├── scripts/
│   ├── init_db.sql
│   └── rls_setup.sql
│
├── migrations/
│
├── tests/
│
├── main.py
│
├── Dockerfile
├── docker-compose.yml
├── pyproject.toml
├── poetry.lock
└── README.md
```

---

# 🚀 Local Development

## Requirements

- Python 3.11+
- PostgreSQL 16
- Docker Desktop
- Poetry

---

## Install Dependencies

```bash
poetry install
```

---

## Start Infrastructure

```bash
docker compose up -d
```

---

## Initialize Database

```bash
docker exec -i chronos_postgres \
psql -U chronos_admin \
-d chronos_ledger_db \
< app/scripts/init_db.sql
```

---

## Run FastAPI

```bash
poetry run uvicorn app.main:app --reload
```

---

## API Documentation

Swagger UI

```text
http://localhost:8000/docs
```

ReDoc

```text
http://localhost:8000/redoc
```

---

# 🧱 Technology Stack

| Layer | Technology |
|--------|------------|
| Backend | FastAPI |
| Language | Python 3.11+ |
| ORM | SQLAlchemy 2.0 Async |
| Driver | AsyncPG |
| Database | PostgreSQL 16 |
| Cache | Redis |
| Authentication | JWT RS256 |
| Migration | Alembic |
| Containerization | Docker |
| Dependency Manager | Poetry |
| API Docs | Swagger / OpenAPI |
| Reverse Proxy | Traefik / Nginx |

---

# 📈 Payroll Processing Flow

```text
Attendance Telemetry
        │
        ▼
Shift Rules
        │
        ▼
Late / Early Calculations
        │
        ▼
Overtime Engine
        │
        ▼
Salary Calculation
        │
        ▼
Tax & Deductions
        │
        ▼
Payroll Ledger
        │
        ▼
Immutable Monthly Statement
```

---

# 🔄 Attendance Flow

```text
Employee Opens App
        │
        ▼
GPS Validation
        │
        ▼
Biometric Validation
        │
        ▼
JWT Authentication
        │
        ▼
Tenant Resolution
        │
        ▼
Attendance API
        │
        ▼
PostgreSQL RLS
        │
        ▼
Attendance Stored
```

---

# 📌 Roadmap

- Multi-Tenant SaaS
- Attendance Engine
- Payroll Engine
- Leave Management
- Shift Planning
- Geo-Fencing
- Face Recognition
- AI Attendance Analytics
- AI Payroll Insights
- Notification Engine
- Audit Logs
- Report Builder
- Mobile Offline Sync
- Multi-Region Deployment
- Kubernetes Support

---

# 👨‍💻 Author

## Santhosh Kannan

**Systems Architect | Mobile & Backend Developer**

Specialized in:

- Enterprise SaaS
- FastAPI
- PostgreSQL
- React Native
- High-Concurrency Backend Systems
- Multi-Tenant Architecture
- Payroll & HRMS Platforms

---

# 📄 License

This project is intended as an enterprise SaaS platform and portfolio showcase.

© 2026 Santhosh Kannan. All rights reserved.    