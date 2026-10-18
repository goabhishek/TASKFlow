# TASKFlow
Full-stack project and task management platform

Problem it solves: Software teams often maintain test cases in spreadsheets, record execution results in different tools, and track defects separately. TESTFlow brings test planning, execution, defect tracking, and quality reporting into one platform.

Main user journey
1. Create an organization and project
Invite team members and assign roles

2. Design test cases and suites
Preconditions, steps, expected results, priorities

3. Create a test plan and run
Select test cases and assign testers

4. Execute and record results
Pass, fail, blocked, skipped, evidence

5. Report defects and measure quality
Trace failures to defects and generate reports

Build these features in order
1. Authentication and project/task CRUD.
2. Role-based access, assignments and comments.
3. Pagination, sorting, search and filtering.
4. Validation, exception handling and database transactions.
5. Unit/integration tests, Docker and CI pipeline.
6. Logging, API documentation and a system architecture diagram.


Technology stack: what to use and why
React + TypeScript on the frontend and Java + Spring Boot on the backend, with PostgreSQL as the database.

Frontend
React + TypeScript + Vite
UI, routing, reusable components and type-safe API integration.
- React Router — navigation and route layouts
- TanStack Query — server state, caching and invalidation
- React Hook Form + Zod — forms and validation
- Tailwind CSS + accessible component primitives — consistent design system
- Vitest + React Testing Library + Playwright — unit, component and end-to-end tests

Backend
Java 21+ + Spring Boot 3.x
REST APIs, business rules, authentication, transactions and reporting.
- Spring Web — REST controllers
- Spring Security — authentication and authorization
- Spring Data JPA + Hibernate — persistence
- Bean Validation — request validation
- Flyway — versioned database migrations
- JUnit 5 + Mockito + Testcontainers — automated backend tests

Database and infrastructure
PostgreSQL + Docker
- PostgreSQL — relational data and transactions
- Docker Compose — local application and database environment
- OpenAPI/Swagger — API documentation
- GitHub Actions — CI pipeline
- Structured logging and Actuator — health checks and observability

Functional modules
Build these modules in order, rather than starting all of them at once.

Module 1 — Identity & Organization
Foundation
Sign in, organizations, invitations, user profiles, roles and project membership.




Module 2 — Project Management
Create projects, set project keys, manage members and configure project settings.




Module 3 — Test Case Management
Create, edit, search, filter and version test cases with steps, preconditions, expected results and priorities.




Module 4 — Test Suites & Plans
Group cases into reusable suites and create plans for releases, sprints or regression cycles.




Module 5 — Test Execution
Create test runs, assign testers and record Pass, Fail, Blocked or Skipped results, along with evidence and comments.




Module 6 — Defect Management
Link failed executions to defects, set severity and priority, assign owners and track defect status.




Module 7 — Dashboard & Reports
Test pass rate, execution progress, failure trends, open defects and project-level quality summaries.




Module 8 — Audit & Activity
Record important actions, such as permission changes, test execution updates and defect transitions.  


Database design
Use PostgreSQL with UUID primary keys, foreign keys, appropriate unique constraints and timestamps. Every tenant-owned record should have an organization_id or an unambiguous relationship to an organization-owned parent.
Core database entities
Suggested starting schema — expand as features require.
Identity
organizations · users · organization_members


Project configuration
projects · project_members


Test design
test_cases · test_steps · test_suites · suite_test_cases · test_plans · plan_test_cases


Execution
test_runs · test_run_cases · execution_results


Defects and history
defects · defect_links · audit_logs · attachments


API design
Use REST endpoints under /api/v1. Return appropriate HTTP status codes, consistent validation errors and pagination metadata.
Module	Example endpoints
Authentication	POST /auth/login, POST /auth/logout
Projects	GET /projects, POST /projects, GET /projects/{id}
Test cases	GET /projects/{id}/test-cases, POST /projects/{id}/test-cases
Suites	POST /projects/{id}/suites, POST /suites/{id}/cases
Test plans	POST /projects/{id}/plans, POST /plans/{id}/cases
Test runs	POST /plans/{id}/runs, POST /runs/{id}/executions
Results	PATCH /executions/{id}/result
Defects	POST /projects/{id}/defects, POST /executions/{id}/defects
Reports	GET /projects/{id}/reports/summary
Audit	GET /projects/{id}/audit-logs


Security architecture
Security should be a core part of TESTFlow from the first milestone, not a final-week feature.
React frontend
Form validation · route UX · no trusted secrets


Spring Security
Authentication · authorization · request validation


Service-level access checks
Organization membership · project permissions · business rules


PostgreSQL
Constraints · least-privilege database account · transactional writes

root structure:
TESTFlow/
├── frontend/
├── backend/
├── infrastructure/
│   └── docker-compose.yml
├── docs/
│   └── architecture.md
├── .gitignore
└── README.md

Create the frontend
npm create vite@latest frontend -- --template react-ts
cd frontend
npm install
npm install react-router-dom @tanstack/react-query
npm install react-hook-form zod @hookform/resolvers
npm run dev


Create the backend
Select these dependencies initially:
- Spring Web
- Spring Data JPA
- Spring Security
- Validation
- PostgreSQL Driver
- Flyway Migration
- Spring Boot Actuator

Add PostgreSQL:  Create infrastructure/docker-compose.yml:
services:
  postgres:
    image: postgres:17
    environment:
      POSTGRES_DB: testflow
      POSTGRES_USER: testflow
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD:?Set POSTGRES_PASSWORD}
    ports:
      - "5432:5432"
    volumes:
      - testflow_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U testflow -d testflow"]
      interval: 5s
      timeout: 3s
      retries: 10

volumes:
  testflow_data:


  et POSTGRES_PASSWORD in your local environment before starting the service. This password is for local development only; never commit credentials or use these development settings in production.
Run:
docker compose -f infrastructure/docker-compose.yml up -d

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/bbc84b4a-7f7e-4f1f-9804-3ce6f66c385f" />




