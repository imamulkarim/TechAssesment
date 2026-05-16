# Tech Assessment - Sponsorship Management System

A comprehensive sponsorship request management system built with .NET 10 (Clean Architecture) and Angular, featuring role-based approvals workflow for managers and finance administrators.

## Table of Contents

- [Overview](#overview)
- [Technology Stack](#technology-stack)
- [Project Structure](#project-structure)
- [Setup Guide](#setup-guide)
- [Running the Application](#running-the-application)
- [Test Login Accounts](#test-login-accounts)
- [API Documentation](#api-documentation)
- [Troubleshooting](#troubleshooting)
- [Development Workflow](#development-workflow)

---

## Overview

The Sponsorship Management System is designed to streamline the sponsorship request approval workflow with the following key features:

- **Requestor Dashboard**: Users can create, submit, and track sponsorship requests
- **Manager Approval**: Managers can review and approve/reject requests at the department level
- **Finance Approval**: Finance administrators can approve/reject requests at the organizational level
- **Admin Dashboard**: System administrators can view all requests and monitor workflow history
- **Role-Based Access**: Built-in role-based authorization (Requestor, Manager, FinanceAdmin, SystemAdmin)
- **Clean Architecture**: Organized with Domain, Application, Infrastructure, and Web layers

The project was generated using the [Clean.Architecture.Solution.Template](https://github.com/jasontaylordev/CleanArchitecture) version 10.8.0.

---

## Technology Stack

### Backend
- **.NET 10** with ASP.NET Core
- **Entity Framework Core** with PostgreSQL
- **MediatR** for CQRS pattern
- **AutoMapper** for DTO mapping
- **Swagger/OpenAPI** for API documentation
- **ASP.NET Identity** for authentication
- **.NET Aspire** (optional) for orchestration

### Frontend
- **Angular 18+** (TypeScript)
- **RxJS** for reactive programming
- **Bootstrap/CSS** for styling
- **NSwag** for TypeScript client generation

### Database
- **PostgreSQL** (primary database)
- **Entity Framework Core** for ORM

---

## 📊 VISUAL WORKFLOW & QUICK START GUIDE

### 🎯 System Overview Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                    SPONSORSHIP WORKFLOW SYSTEM                  │
└─────────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────────┐
│                          REQUESTOR                               │
├──────────────────────────────────────────────────────────────────┤
│  1. Create Request (Draft)                                       │
│  2. Edit & Save Draft                                            │
│  3. Submit for Approval → PendingManagerApproval                 │
│  4. View Status & History                                        │
│  5. Cancel (if not approved)                                     │
└──────────────────────────────────────────────────────────────────┘
                              ↓
                    ┌─────────────────┐
                    │    MANAGER      │
                    ├─────────────────┤
                    │  Review Request │
                    │  - Approve      │→ PendingFinanceReview
                    │  - Reject       │→ Rejected
                    └─────────────────┘
                              ↓ (if approved)
                    ┌─────────────────┐
                    │  FINANCE ADMIN  │
                    ├─────────────────┤
                    │  Final Review   │
                    │  - Approve      │→ Approved ✓
                    │  - Reject       │→ Rejected ✗
                    └─────────────────┘
                              ↓
                    ┌─────────────────┐
                    │  SYSTEM ADMIN   │
                    ├─────────────────┤
                    │  View All       │
                    │  View History   │
                    │  Export Data    │
                    └─────────────────┘
```

---

## 🔄 Data Flow Diagram

```
FRONTEND (Angular)                 BACKEND (.NET 10)
━━━━━━━━━━━━━━━━━━━━━━━━          ━━━━━━━━━━━━━━━━━━

┌─────────────────┐                ┌──────────────┐
│  Components     │                │  Endpoints   │
│  Dashboard      │────────────────│  GET/POST    │
│  Form           │   HTTP/REST    │  PUT/DELETE  │
│  Approval List  │────────────────│              │
│  Detail         │                └──────┬───────┘
│  Admin View     │                       │
└────────┬────────┘                       ↓
         │                        ┌──────────────────┐
         │                        │ MediatR Pipeline │
         │                        │ - Validators     │
         │                        │ - Behaviors      │
         │                        │ - Handlers       │
         │                        └──────┬───────────┘
         │                               ↓
         │                        ┌──────────────────┐
         ↓                        │ Application      │
    ┌─────────────┐              │ Commands/Queries │
    │ RxJS        │              └──────┬───────────┘
    │ Services    │                     ↓
    │ TypeScript  │              ┌──────────────────┐
    └─────────────┘              │ Domain Logic     │
                                 │ Entity Framework │
                                 │ Repositories     │
                                 └──────┬───────────┘
                                        ↓
                                 ┌──────────────────┐
                                 │ PostgreSQL DB    │
                                 │ - Requests       │
                                 │ - Approvals      │
                                 │ - Types          │
                                 └──────────────────┘
```

---

## Project Structure

```
TechAssessment/
├── src/
│   ├── Web/                          # ASP.NET Core Web API
│   │   ├── Program.cs               # Application startup
│   │   ├── Endpoints/               # Minimal API endpoints
│   │   ├── Infrastructure/          # Web-specific infrastructure
│   │   └── ClientApp/               # Angular frontend
│   │       ├── src/
│   │       │   ├── app/
│   │       │   │   ├── models/      # TypeScript models
│   │       │   │   ├── services/    # Angular services
│   │       │   │   ├── sponsorship/ # Feature modules
│   │       │   │   └── web-api-client.ts  # Generated client
│   │       │   └── main.ts
│   │       ├── angular.json
│   │       ├── package.json
│   │       └── nswag.json          # API client generation config
│   ├── Application/                 # Business logic (CQRS)
│   │   ├── Commands/               # Command handlers
│   │   └── Queries/                # Query handlers
│   ├── Domain/                      # Domain entities
│   │   ├── Entities/               # Business domain models
│   │   ├── Events/                 # Domain events
│   │   └── Enums/
│   ├── Infrastructure/              # Data access & external services
│   │   ├── Data/                   # EF configurations
│   │   └── Identity/               # Authentication setup
│   └── Shared/                      # Shared DTOs & utilities
├── AppHost/                         # .NET Aspire orchestration (optional)
└── ServiceDefaults/                 # Aspire service defaults
```

---

## Setup Guide

### Prerequisites

Before starting, ensure you have the following installed:

1. **.NET 10 SDK**
   - Download from: https://dotnet.microsoft.com/download/dotnet/10.0
   - Verify installation: `dotnet --version`

2. **PostgreSQL** (version 12 or higher)
   - Download from: https://www.postgresql.org/download/
   - Verify installation: `psql --version`
   - Ensure PostgreSQL service is running

3. **Node.js & npm** (for Angular development)
   - Download from: https://nodejs.org/
   - Verify installation: `node --version` and `npm --version`

4. **Visual Studio Community 2026** (or VS Code)
   - Recommended for .NET development

5. **Git** (for version control)
   - Download from: https://git-scm.com/

### Step 1: Clone the Repository

```bash
git clone <repository-url>
cd TechAssessment
```

### Step 2: Database Setup

#### Create PostgreSQL Database

1. **Open PostgreSQL Command Line:**
   ```bash
   psql -U postgres
   ```

2. **Create database:**
   ```sql
   CREATE DATABASE tech_assessment;
   ```

3. **Create dedicated user (optional but recommended):**
   ```sql
   CREATE USER assessment_user WITH PASSWORD 'Assessment@123';
   ALTER ROLE assessment_user SET client_encoding TO 'utf8';
   ALTER ROLE assessment_user SET default_transaction_isolation TO 'read committed';
   GRANT ALL PRIVILEGES ON DATABASE tech_assessment TO assessment_user;
   ```

#### Configure Connection String

Update `src/Web/appsettings.json` with your database credentials:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Host=localhost;Port=5432;Database=tech_assessment;Username=assessment_user;Password=Assessment@123"
  }
}
```

Or if using default postgres user:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Host=localhost;Port=5432;Database=tech_assessment;Username=postgres;Password=<your-postgres-password>"
  }
}
```

### Step 3: Initialize Database with EF Core Migrations

```bash
cd src/Web

# Apply migrations and create database schema
dotnet ef database update

# Or from repository root:
dotnet ef database update --project src/Web
```

This will:
- Create all database tables
- Seed initial roles (Requestor, Manager, FinanceAdmin, SystemAdmin)
- Set up ASP.NET Identity tables
- Initialize test user accounts

### Step 4: Angular Frontend Setup

```bash
cd src/Web/ClientApp

# Install npm dependencies
npm install

# (Optional) Generate/regenerate TypeScript client from Swagger
npm run generate-client
```

### Step 5: Build the Solution

```bash
# From repository root
dotnet build

# Expected output: Build succeeded
```

---

## Running the Application

### Option 1: Direct WebAPI + Angular 

This approach runs the backend and frontend separately, giving you maximum control and faster feedback during development.

#### Terminal 1: Start Backend WebAPI

```bash
cd src/Web

# Run the backend with hot reload
dotnet run

# Expected output:
# info: Microsoft.Hosting.Lifetime[14]
#       Now listening on: https://localhost:7081
#       Now listening on: http://localhost:5000
```

**Verify Backend is Running:**
- API Health: https://localhost:7081/health (if configured)
- Swagger UI: https://localhost:7081/swagger/ui
- API Base URL: https://localhost:7081/api

#### Terminal 2: Start Angular Frontend

```bash
cd src/Web/ClientApp

# Start Angular development server with hot reload
npm start

# Expected output:
# ✔ Compiled successfully.
# ✔ Compiled successfully in 5.23s.
# ○ Generating browser application bundles...
# 
# ✔ Build successful.
```

**Verify Frontend is Running:**
- Application URL: http://localhost:4200
- DevTools: Press F12 to check console for errors

#### Access the Application

1. Open browser to `http://localhost:4200`
2. You'll be redirected to login page
3. Enter test credentials (see [Test Login Accounts](#test-login-accounts))
4. Login and navigate through the application

#### Stop the Services

- **Backend**: Press `Ctrl + C` in Terminal 1
- **Frontend**: Press `Ctrl + C` in Terminal 2

---

### Option 2: Using .NET Aspire Orchestration (Recommended for Development)

This approach uses .NET Aspire to orchestrate all services (database, API, frontend) with centralized dashboard.

#### Prerequisites for Aspire

1. **Install Aspire workload** (if not already installed):
   ```bash
   dotnet workload update
   dotnet workload install aspire
   ```

2. **Verify Aspire installation:**
   ```bash
   dotnet workload list
   # Should show: aspire (active)
   ```

#### Step 1: Verify Aspire Configuration

Ensure `AppHost/Program.cs` contains:

```csharp
var builder = DistributedApplication.CreateBuilder(args);

var postgres = builder.AddPostgres("postgres")
    .WithPgAdmin()
    .AddDatabase("techassessment");

var api = builder.AddProject<Projects.TechAssessment_Web>("api")
    .WithReference(postgres)
    .WaitFor(postgres);

builder.AddNpmApp("frontend", "../src/Web/ClientApp")
    .WithReference(api)
    .WaitFor(api)
    .WithEnvironment("API_BASE_URL", "http://localhost:5000");

builder.Build().Run();
```

#### Step 2: Run with Aspire

```bash
# From repository root
dotnet run --project .\AppHost

# Expected output:
# info: Aspire.Hosting[0]
#       Aspire dashboard available at https://localhost:18888
#
# Starting resource "postgres"...
# Starting resource "api"...
# Starting resource "frontend"...
```

#### Step 3: Access Aspire Dashboard

1. Open browser to `https://localhost:18888`
2. **Dashboard Features:**
   - **Resources Tab**: Shows all running services (postgres, api, frontend)
   - **Logs Tab**: Real-time logs from each service
   - **Metrics Tab**: Performance monitoring (CPU, memory, requests)
   - **Traces Tab**: Distributed tracing for API calls

#### Service URLs (with Aspire)

- **Aspire Dashboard**: https://localhost:18888
- **Frontend**: http://localhost:4200 (shown in dashboard)
- **API**: https://localhost:7081 (shown in dashboard)
- **Swagger UI**: https://localhost:7081/swagger/ui
- **PgAdmin**: http://localhost:5050 (database admin)

#### Viewing Logs in Aspire

1. Open Aspire Dashboard: https://localhost:18888
2. Click on a resource (api, frontend, postgres)
3. Click "Logs" to see real-time output
4. Search/filter logs by service or message

#### Stop Aspire

Press `Ctrl + C` in the terminal to stop all services.

---

## Test Login Accounts

### Pre-seeded Test Users

The database initialization script automatically creates the following test users. Use these to test different role-based workflows:

| Username | Password | Role(s) | Purpose |
|----------|----------|---------|---------|
| `requestor@example.com` | `Requestor1!` | Requestor | Create and submit sponsorship requests |
| `manager@example.com` | `Manager1!` | Manager | Approve/reject manager-level requests |
| `finance@example.com` | `Finance1!` | FinanceAdmin | Approve/reject finance-level requests |
| `admin@example.com` | `Administrator1!` | SystemAdmin | View all requests, monitor workflow |

### Login Steps

1. Navigate to `http://localhost:4200`
2. Enter credentials from table above
3. Click **Sign In** button
4. Application redirects to role-specific dashboard

### Feature Access by Role

#### Requestor (`requestor@example.com`)
- **Dashboard**: View own submitted sponsorship requests
- **Create New**: Create new sponsorship requests
- **Edit Draft**: Modify requests in draft status
- **Submit Request**: Submit draft requests for approval
- **Track Status**: Monitor approval progress through workflow
- **Cancel Request**: Cancel pending or rejected requests

#### Manager (`manager@example.com`)
- **Manager Approvals**: View requests pending manager approval
- **Approve/Reject**: Make approval decisions with optional comments
- **View Details**: See full request details before deciding
- **Workflow History**: Track approval workflow for each request
- **Cannot**: Create requests or access finance approval

#### Finance Admin (`finance@example.com`)
- **Finance Approvals**: View requests pending finance approval
- **Approve/Reject**: Make final finance approval decisions
- **View Details**: See full request details including manager comments
- **Workflow History**: View complete approval workflow
- **Cannot**: Create requests or access manager approval

#### System Admin (`admin@example.com`)
- **Admin Dashboard**: View ALL requests in the system
- **Workflow History**: View complete workflow history for any request
- **System Access**: Full system access and monitoring
- **Reports Ready**: System prepared for admin reporting (future feature)

### Testing Workflow

To test the complete approval workflow:

1. **Login as Requestor**
   - Create a new sponsorship request
   - Fill in all details (title, department, type, amount, etc.)
   - Click "Save as Draft"
   - Click "Submit Request"

2. **Login as Manager**
   - Go to Manager Approvals
   - Find the submitted request
   - Click to review details
   - Click "Approve" with optional comment
   - Return to dashboard to confirm submission

3. **Login as Finance Admin**
   - Go to Finance Approvals
   - Find the manager-approved request
   - Click to review details and manager comments
   - Click "Approve" to finalize
   - Return to dashboard

4. **Login as Admin**
   - Go to Admin Dashboard
   - Find the request you just processed
   - Click to view complete workflow history
   - See all approvals and comments

---

## API Documentation

### Swagger UI

Interactive API documentation available at:

```
https://techasses-e5e0f6e0g8hrbmab.southeastasia-01.azurewebsites.net/swagger/index.html
```

**Swagger Features:**
- View all available endpoints
- Test API calls directly
- See request/response schemas
- Try out endpoints with different parameters

### API Base URL

```
https://techasses-e5e0f6e0g8hrbmab.southeastasia-01.azurewebsites.net/api
```

### FrontEnd URL

```
https://techasses-e5e0f6e0g8hrbmab.southeastasia-01.azurewebsites.net/
```

## Help
To learn more about the template go to the [project website](https://cleanarchitecture.jasontaylor.dev). Here you can find additional guidance, request new features, report a bug, and discuss the template with other users.
