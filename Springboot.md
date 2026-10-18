

## Table of Contents

1. [What is Spring Boot?](#1-what-is-spring-boot)
2. [Prerequisites and installation](#2-prerequisites-and-installation)
3. [Create the first project](#3-create-the-first-project)
4. [Understand the project structure](#4-understand-the-project-structure)
5. [Run and configure the application](#5-run-and-configure-the-application)
6. [Core Spring concepts](#6-core-spring-concepts)
7. [Build REST APIs](#7-build-rest-apis)
8. [DTOs and validation](#8-dtos-and-validation)
9. [Global exception handling](#9-global-exception-handling)
10. [PostgreSQL setup and integration](#10-postgresql-setup-and-integration)
11. [JPA and Hibernate](#11-jpa-and-hibernate)
12. [Transactions and database migrations](#12-transactions-and-database-migrations)
13. [Spring Security](#13-spring-security)
14. [JWT authentication design](#14-jwt-authentication-design)
15. [CORS, CSRF, secrets, and security checklist](#15-cors-csrf-secrets-and-security-checklist)
16. [Testing](#16-testing)
17. [Logging and monitoring](#17-logging-and-monitoring)
18. [Caching, async, and scheduling](#18-caching-async-and-scheduling)
19. [Architecture and design patterns](#19-architecture-and-design-patterns)
20. [React integration](#20-react-integration)
21. [Docker and local development](#21-docker-and-local-development)
22. [Deployment](#22-deployment)
23. [CI/CD example](#23-cicd-example)
24. [Performance and production readiness](#24-performance-and-production-readiness)
25. [Common errors and fixes](#25-common-errors-and-fixes)
26. [Interview questions and short answers](#26-interview-questions-and-short-answers)
27. [Practice roadmap](#27-practice-roadmap)
28. [Official references](#28-official-references)

---

## 1. What is Spring Boot?

Spring Boot is built on the Spring Framework and simplifies creating production-oriented Java applications.

### Main features

- Auto-configuration based on dependencies and application configuration.
- Starter dependencies that group commonly used libraries.
- Embedded web servers, such as Tomcat for common servlet-based applications.
- Externalized configuration through properties, YAML, environment variables, and profiles.
- Integration with testing, metrics, health checks, security, and data-access libraries.

### Spring vs Spring Boot

- **Spring Framework:** core features such as dependency injection, transaction management, and Spring MVC.
- **Spring Boot:** conventions and tools that simplify configuring and running Spring applications.

### Typical request flow

```text
React browser
    |
    | HTTP request / JSON
    v
Controller -> Service -> Repository -> PostgreSQL
    ^             |           |
    |             |           |
    +------ JSON response ----+
```

The controller handles the HTTP boundary, the service contains business rules, the repository performs data access, and PostgreSQL persists data.

---

## 2. Prerequisites and installation

### 2.1 Install Java JDK

Install a supported JDK, for example JDK 21. A JDK includes the compiler and development tools.

Verify the installation:

```bash
java -version
javac -version
```

On Windows PowerShell, check the environment:

```powershell
$env:JAVA_HOME
where.exe java
```

On macOS/Linux:

```bash
echo "$JAVA_HOME"
which java
```

If the commands fail, install a JDK and configure `JAVA_HOME` and `PATH` according to the JDK vendor's instructions. Restart the terminal after changing environment variables.

### 2.2 Install an IDE

Choose one:

- IntelliJ IDEA: install the IDE and select the JDK for the project.
- Visual Studio Code: install the Java extension pack and Spring Boot extensions.
- Eclipse with Spring Tools: another valid Java/Spring development option.

### 2.3 Install Maven

Spring Initializr projects commonly include the Maven Wrapper (`mvnw` or `mvnw.cmd`), so a separate global Maven install is not always necessary.

Verify a global Maven installation if you use one:

```bash
mvn -version
```

Prefer the project wrapper for repeatable builds:

```bash
# Windows
mvnw.cmd -version

# macOS/Linux
./mvnw -version
```

### 2.4 Install Git

```bash
git --version
```

### 2.5 Install PostgreSQL

Install PostgreSQL from the official PostgreSQL download page, or use Docker as described later. Remember the database name, port, username, and password you configure.

Verify with `psql` if the command-line client is installed:

```bash
psql --version
```

### 2.6 Optional tools

- Postman, Insomnia, or `curl` for API testing.
- Docker Desktop or Docker Engine.
- A database client such as pgAdmin or DBeaver.
- Node.js and npm if building a React frontend.

---

## 3. Create the first project

### Option A: Spring Initializr

Open [https://start.spring.io/](https://start.spring.io/) and choose:

- Project: Maven
- Language: Java
- Spring Boot: a currently supported release
- Packaging: Jar
- Java: a version supported by that Spring Boot release, such as 21

For a REST API with PostgreSQL, add:

- Spring Web
- Spring Data JPA
- PostgreSQL Driver
- Validation
- Spring Security (when you are ready to configure security)
- Spring Boot Actuator
- Spring Boot Starter Test

Download the ZIP and extract it.

> If Spring Security is added before configuring a security filter chain, Spring Boot applies its default security behavior and may require authentication for requests. This is expected.

### Option B: Start from a Maven project

Do not copy a random `pom.xml` version from an old tutorial. Generate the project using Spring Initializr and retain the generated Spring Boot parent/plugin versions.

Run the app:

```bash
# Windows
mvnw.cmd spring-boot:run

# macOS/Linux
./mvnw spring-boot:run
```

Build the JAR:

```bash
# Windows
mvnw.cmd clean package

# macOS/Linux
./mvnw clean package
```

Run the packaged application:

```bash
java -jar target/your-generated-artifact-name.jar
```

---

## 4. Understand the project structure

A typical project:

```text
src/
  main/
    java/
      com/example/testflow/
        TestflowApplication.java
        controller/
        service/
        repository/
        entity/
        dto/
        exception/
        config/
        security/
    resources/
      application.properties
      db/migration/
  test/
    java/
      com/example/testflow/
pom.xml
mvnw
mvnw.cmd
.gitignore
```

### Main application class

```java
package com.example.testflow;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class TestflowApplication {
    public static void main(String[] args) {
        SpringApplication.run(TestflowApplication.class, args);
    }
}
```

`@SpringBootApplication` combines configuration, auto-configuration, and component scanning.

Keep your application packages under the main application package, or configure scanning deliberately.

---

## 5. Run and configure the application

### 5.1 `application.properties`

```properties
spring.application.name=testflow
server.port=8080
```

Alternative YAML configuration (`application.yml`):

```yaml
spring:
  application:
    name: testflow
server:
  port: 8080
```

Use either format; do not maintain conflicting values in both without a clear reason.

### 5.2 Profiles

Use separate configuration for development, testing, and production.

Example `application-dev.properties`:

```properties
server.port=8080
logging.level.com.example.testflow=DEBUG
```

Example `application-prod.properties`:

```properties
server.port=${PORT:8080}
logging.level.root=INFO
```

Activate a profile through an environment variable or command-line option:

```bash
SPRING_PROFILES_ACTIVE=dev ./mvnw spring-boot:run
```

PowerShell:

```powershell
$env:SPRING_PROFILES_ACTIVE="dev"
.\mvnw.cmd spring-boot:run
```

Do not put production passwords, private keys, or JWT secrets in committed configuration files.

---

## 6. Core Spring concepts

### 6.1 Inversion of Control (IoC)

The framework manages object creation and wiring instead of application code manually creating every dependency.

### 6.2 Dependency Injection (DI)

Prefer constructor injection because dependencies are explicit and can be `final`.

```java
@Service
public class GreetingService {
    public String greeting() {
        return "Hello from Spring Boot";
    }
}

@RestController
@RequestMapping("/api")
public class GreetingController {
    private final GreetingService greetingService;

    public GreetingController(GreetingService greetingService) {
        this.greetingService = greetingService;
    }

    @GetMapping("/hello")
    public Map<String, String> hello() {
        return Map.of("message", greetingService.greeting());
    }
}
```

Add `import java.util.Map;` to the controller.

### 6.3 Beans and annotations

- `@Component`: generic Spring-managed component.
- `@Service`: service/business logic.
- `@Repository`: persistence component; also participates in persistence exception translation.
- `@Controller`: MVC controller.
- `@RestController`: controller whose methods write response bodies by default.
- `@Configuration`: configuration class.
- `@Bean`: declares an object from a configuration method.
- `@Autowired`: dependency injection annotation; a single constructor often does not require it.
- `@Qualifier`: selects a particular bean when multiple candidates exist.
- `@Primary`: marks a preferred candidate.

### 6.4 Bean scope

- Singleton: one bean instance per Spring container by default.
- Prototype: a new instance is created when requested from the container.
- Web scopes such as request/session are available in appropriate web applications.

A singleton bean should not keep unsynchronized mutable per-request state in instance fields.

### 6.5 Auto-configuration

Spring Boot configures components based on the classpath, existing beans, and properties. Configuration can be customized or overridden; understand what the starter dependencies bring into the application.

---

## 7. Build REST APIs

### 7.1 HTTP methods

| Method | Typical purpose |
|---|---|
| GET | Read a resource |
| POST | Create a resource or initiate an operation |
| PUT | Replace a resource |
| PATCH | Partially update a resource |
| DELETE | Delete a resource |

Common status codes:

- `200 OK`: successful request.
- `201 Created`: resource created.
- `204 No Content`: successful request without response body.
- `400 Bad Request`: invalid request.
- `401 Unauthorized`: authentication is missing or invalid.
- `403 Forbidden`: caller is not permitted.
- `404 Not Found`: resource does not exist.
- `409 Conflict`: conflict with current resource state.
- `500 Internal Server Error`: unexpected server-side failure.

### 7.2 Controller example

```java
@RestController
@RequestMapping("/api/greetings")
public class GreetingController {

    @GetMapping
    public Map<String, String> getGreeting(
            @RequestParam(defaultValue = "Abhi") String name) {
        return Map.of("message", "Hello, " + name);
    }
}
```

Test:

```bash
curl "http://localhost:8080/api/greetings?name=Abhi"
```

Expected JSON:

```json
{"message":"Hello, Abhi"}
```

### 7.3 Request mapping annotations

- `@RequestMapping`: shared path and mapping configuration.
- `@GetMapping`, `@PostMapping`, `@PutMapping`, `@PatchMapping`, `@DeleteMapping`: HTTP-specific mappings.
- `@PathVariable`: reads a value from a path such as `/users/{id}`.
- `@RequestParam`: reads query parameters.
- `@RequestBody`: converts a request body, often JSON, into a Java object.
- `ResponseEntity<T>`: controls status, headers, and body.

### 7.4 REST design best practices

- Use resource-oriented paths such as `/api/users/{id}`.
- Use DTOs rather than exposing database entities directly.
- Validate all untrusted input.
- Return consistent error responses.
- Add pagination to potentially large collections.
- Document APIs using OpenAPI where appropriate.
- Keep authorization on the server, not only in the frontend.

---

## 8. DTOs and validation

DTOs (Data Transfer Objects) define the request/response shape and separate the API contract from the persistence model.

### Request DTO

```java
package com.example.testflow.dto;

import jakarta.validation.constraints.Email;
import jakarta.validation.constraints.NotBlank;
import jakarta.validation.constraints.Size;

public record CreateUserRequest(
    @NotBlank @Size(max = 100) String name,
    @NotBlank @Email @Size(max = 255) String email
) {}
```

### Controller

```java
@PostMapping("/users")
public ResponseEntity<Map<String, String>> createUser(
        @Valid @RequestBody CreateUserRequest request) {
    // Call a service and persist the user in a real application.
    return ResponseEntity.status(HttpStatus.CREATED)
        .body(Map.of("name", request.name(), "email", request.email()));
}
```

Imports:

```java
import jakarta.validation.Valid;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import java.util.Map;
```

Include the Spring Boot validation starter in the project.

Useful validation annotations:

- `@NotNull`, `@NotBlank`, `@NotEmpty`
- `@Size`
- `@Email`
- `@Min`, `@Max`
- `@Positive`

Validation is not authorization. Validate input shape and values, and separately verify whether the current caller is permitted to perform the operation.

---

## 9. Global exception handling

Create application-specific exceptions instead of leaking internal stack traces.

### Custom exception

```java
public class UserNotFoundException extends RuntimeException {
    public UserNotFoundException(Long id) {
        super("User " + id + " was not found");
    }
}
```

### Error response

```java
public record ApiError(String code, String message) {}
```

### Global handler

```java
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<ApiError> handleNotFound(
            UserNotFoundException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND)
            .body(new ApiError("USER_NOT_FOUND", ex.getMessage()));
    }
}
```

For a production API, also handle validation failures and known conflicts consistently. Log unexpected exceptions with a correlation ID, but do not return stack traces, SQL, secrets, or internal implementation details to clients.

---

## 10. PostgreSQL setup and integration

### 10.1 Create a database

Using `psql` as a database administrator:

```sql
CREATE DATABASE testflow;
```

Create a dedicated application role with a strong password and only the permissions required by the application. Avoid using the PostgreSQL superuser from an application.

Example (run as an authorized database administrator and replace the password securely):

```sql
CREATE ROLE testflow_app LOGIN PASSWORD 'replace-with-a-strong-secret';
GRANT CONNECT ON DATABASE testflow TO testflow_app;
```

Connect to the `testflow` database and grant appropriate schema/table privileges. In a production setup, separate migration permissions from the runtime application's permissions where practical.

### 10.2 Add dependencies

Use Spring Initializr or ensure the Maven project includes:

- Spring Data JPA starter.
- PostgreSQL JDBC driver.
- Database migration tool such as Flyway (recommended for managed schema changes).

Use versions managed by your Spring Boot dependency management unless there is a deliberate compatibility reason not to.

### 10.3 Configure the connection

```properties
spring.datasource.url=${DB_URL:jdbc:postgresql://localhost:5432/testflow}
spring.datasource.username=${DB_USERNAME:testflow_app}
spring.datasource.password=${DB_PASSWORD}
spring.jpa.hibernate.ddl-auto=validate
spring.jpa.open-in-view=false
```

The `DB_PASSWORD` environment variable must be set. Do not commit credentials.

### 10.4 Verify connectivity

Start PostgreSQL, configure environment variables, then run the Spring Boot app. Check startup logs for connection or schema validation errors. Do not log passwords or complete secret-bearing connection strings.

---

## 11. JPA and Hibernate

### 11.1 Key concepts

- **JPA:** Java persistence specification.
- **Hibernate:** common implementation of JPA.
- **Spring Data JPA:** repository abstraction that reduces persistence boilerplate.
- **Entity:** Java class mapped to a database table.
- **Repository:** interface for persistence operations.
- **Persistence context:** tracks managed entities and their changes.
- **Dirty checking:** managed entity changes can be synchronized to the database at flush/commit.

### 11.2 Entity example

```java
package com.example.testflow.entity;

import jakarta.persistence.*;

@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, length = 100)
    private String name;

    @Column(nullable = false, unique = true, length = 255)
    private String email;

    protected User() {}

    public User(String name, String email) {
        this.name = name;
        this.email = email;
    }

    public Long getId() { return id; }
    public String getName() { return name; }
    public String getEmail() { return email; }

    public void setName(String name) { this.name = name; }
}
```

In a larger project, make entity mutation methods deliberate and enforce invariants. Ensure database constraints match important application rules.

### 11.3 Repository

```java
import org.springframework.data.jpa.repository.JpaRepository;
import java.util.Optional;

public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByEmailIgnoreCase(String email);
    boolean existsByEmailIgnoreCase(String email);
}
```

### 11.4 Service layer

```java
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;
import java.util.List;

@Service
public class UserService {
    private final UserRepository repository;

    public UserService(UserRepository repository) {
        this.repository = repository;
    }

    @Transactional(readOnly = true)
    public List<User> findAll() {
        return repository.findAll();
    }

    @Transactional
    public User create(String name, String email) {
        if (repository.existsByEmailIgnoreCase(email)) {
            throw new IllegalArgumentException("Email already exists");
        }
        return repository.save(new User(name, email));
    }
}
```

A database unique constraint remains essential because application-level checks can race under concurrent requests. In production, map unique-constraint violations to a suitable `409 Conflict` response.

### 11.5 Relationships

- `@ManyToOne`: many records reference one parent.
- `@OneToMany`: one parent relates to many records.
- `@OneToOne`: one-to-one association.
- `@ManyToMany`: many-to-many association, often best modeled explicitly with a join entity when the relationship has its own attributes.

Understand owning side, `mappedBy`, cascade behavior, orphan removal, lazy/eager fetching, and JSON serialization cycles. Avoid blindly adding `CascadeType.ALL` or switching everything to eager fetching.

### 11.6 Pagination

```java
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Sort;

Page<User> page = repository.findAll(
    PageRequest.of(0, 20, Sort.by("id").ascending())
);
```

Validate page size and allowed sort fields in a real endpoint.

### 11.7 Avoid the N+1 query problem

An N+1 issue occurs when loading a collection of entities causes additional queries for related entities. Investigate SQL logs and query plans; use fetch joins, entity graphs, projections, or batch fetching where appropriate. Choose based on actual access patterns and avoid fetching huge graphs unnecessarily.

---

## 12. Transactions and database migrations

### 12.1 `@Transactional`

Use a transaction around a business operation that must succeed or fail as a unit.

```java
@Service
public class TransferService {
    private final AccountRepository repository;

    public TransferService(AccountRepository repository) {
        this.repository = repository;
    }

    @Transactional
    public void transfer(Long fromId, Long toId, BigDecimal amount) {
        if (amount.signum() <= 0) {
            throw new IllegalArgumentException("Amount must be positive");
        }

        Account from = repository.findById(fromId)
            .orElseThrow(() -> new IllegalArgumentException("Source not found"));
        Account to = repository.findById(toId)
            .orElseThrow(() -> new IllegalArgumentException("Target not found"));

        if (from.getBalance().compareTo(amount) < 0) {
            throw new IllegalStateException("Insufficient funds");
        }

        from.debit(amount);
        to.credit(amount);
    }
}
```

This illustrates transaction boundaries; a real transfer service also needs concurrency controls, authorization, audit records, idempotency considerations, and correct handling of simultaneous transfers.

Important topics:

- Default rollback behavior commonly applies to unchecked exceptions and `Error`; checked-exception rollback rules can be configured.
- Default proxy-based transactions generally apply to calls entering through the Spring proxy; self-invocation can bypass the proxy.
- Learn propagation and isolation settings.
- Keep transactions short and avoid slow network calls inside database transactions when possible.

### 12.2 Database migrations

Use versioned migrations with Flyway or Liquibase to manage schema changes.

Example Flyway migration `src/main/resources/db/migration/V1__create_users.sql`:

```sql
CREATE TABLE users (
    id BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE
);
```

For an application using migrations, set Hibernate schema behavior deliberately, commonly:

```properties
spring.jpa.hibernate.ddl-auto=validate
```

Do not use destructive schema generation in production. Test migrations against a disposable database before deployment.

---

## 13. Spring Security

### 13.1 Core terms

- **Authentication:** who the caller is.
- **Authorization:** what the caller is allowed to do.
- **Principal:** representation of the authenticated identity.
- **Authority/role:** permission information used by access rules.
- **Security filter chain:** filters that process requests before they reach controllers.

### 13.2 Add Spring Security

Add the Spring Boot Security starter. Once present, verify the default security behavior before exposing endpoints.

### 13.3 Define authorization rules

This example makes health and login paths public and requires authentication for the API. Adapt the public paths and login design to your application.

```java
package com.example.testflow.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.HttpMethod;
import org.springframework.security.config.Customizer;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.web.SecurityFilterChain;

@Configuration
public class SecurityConfig {

    @Bean
    SecurityFilterChain securityFilterChain(HttpSecurity http)
            throws Exception {
        http
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/actuator/health").permitAll()
                .requestMatchers(HttpMethod.GET, "/api/public/**").permitAll()
                .anyRequest().authenticated()
            )
            .httpBasic(Customizer.withDefaults());

        return http.build();
    }
}
```

This demonstrates URL authorization with HTTP Basic. Use HTTPS and a deliberate authentication design in real deployments. For a stateless bearer-token API, configure an OAuth2 Resource Server with JWT validation instead of treating this snippet as a complete JWT implementation.

### 13.4 Password hashing

Never store plain-text passwords. Use a password hashing function such as BCrypt through Spring Security's `PasswordEncoder`.

```java
@Bean
PasswordEncoder passwordEncoder() {
    return new BCryptPasswordEncoder();
}
```

Store the encoded hash, not the original password. Never log credentials or send passwords back in API responses.

### 13.5 Method security

Enable method security where required:

```java
@Configuration
@EnableMethodSecurity
public class MethodSecurityConfig {}
```

Then protect service methods or controller operations using annotations such as `@PreAuthorize`, for example:

```java
@PreAuthorize("hasRole('ADMIN')")
public void deleteUser(Long userId) {
    // Perform authorized deletion.
}
```

A role-based example is not a replacement for verifying resource ownership and tenant boundaries.

---

## 14. JWT authentication design

JWT is a token format, not a complete authentication system by itself.

### Typical flow

1. A user authenticates with a trusted authentication endpoint or identity provider.
2. The system issues a short-lived access token after successful authentication.
3. The client sends `Authorization: Bearer <token>` to protected APIs.
4. The API validates the signature, issuer, expiration, and relevant claims.
5. The API authorizes the requested action using validated identity and permissions.
6. Token renewal and logout/revocation behavior are designed explicitly.

### Use Spring Security OAuth2 Resource Server

For a standards-based resource server, use Spring Security's OAuth2 Resource Server support rather than writing custom token verification from scratch.

Typical properties when using a trusted issuer:

```properties
spring.security.oauth2.resourceserver.jwt.issuer-uri=${JWT_ISSUER_URI}
```

The exact configuration depends on your identity provider, signing keys, audience requirements, and Spring Security version. Validate the expected audience where your architecture requires it.

### JWT security checklist

- Use a trusted, maintained library/provider.
- Validate signatures and expected algorithms.
- Validate issuer and audience where applicable.
- Enforce expiry and appropriate clock-skew rules.
- Keep access tokens short-lived.
- Plan refresh-token rotation/revocation if refresh tokens are used.
- Do not put passwords or sensitive personal data in JWT claims.
- Do not assume a decoded token is trustworthy.
- Protect signing keys and secrets.
- Define logout semantics; a self-contained token is not automatically revoked when a user logs out.
- Do not store long-lived bearer tokens in browser local storage without assessing the XSS risk.
- Consider secure, HttpOnly, SameSite cookies and CSRF defenses when using cookie-based authentication.

---

## 15. CORS, CSRF, secrets, and security checklist

### 15.1 CORS

CORS controls which browser origins may read cross-origin responses. It is not an authorization mechanism.

Example configuration:

```java
@Bean
CorsConfigurationSource corsConfigurationSource() {
    CorsConfiguration configuration = new CorsConfiguration();
    configuration.setAllowedOrigins(List.of("http://localhost:5173"));
    configuration.setAllowedMethods(List.of("GET", "POST", "PUT", "PATCH", "DELETE"));
    configuration.setAllowedHeaders(List.of("Authorization", "Content-Type"));
    configuration.setAllowCredentials(false);

    UrlBasedCorsConfigurationSource source =
        new UrlBasedCorsConfigurationSource();
    source.registerCorsConfiguration("/api/**", configuration);
    return source;
}
```

Imports:

```java
import java.util.List;
import org.springframework.context.annotation.Bean;
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.CorsConfigurationSource;
import org.springframework.web.cors.UrlBasedCorsConfigurationSource;
```

Connect this source to the Spring Security CORS configuration if security is enabled:

```java
http.cors(Customizer.withDefaults());
```

For cookie-based credentials, configure an explicit allowed origin and credential policy. Do not combine wildcard origins with credentialed CORS.

### 15.2 CSRF

CSRF risk depends on how credentials are sent. Browser-managed cookies are automatically attached to requests and therefore require careful CSRF protection. Disabling CSRF may be appropriate for a deliberately stateless bearer-token API, but it is not a universal Spring Security recommendation.

### 15.3 Secrets management

- Keep credentials out of Git.
- Use environment variables for local development.
- Use a secret manager or platform secret facility in production.
- Rotate compromised credentials.
- Use least-privilege database roles.
- Never expose server secrets through frontend environment variables or API responses.

### 15.4 Production security checklist

- HTTPS enabled end-to-end.
- Authentication and authorization tested.
- Request validation and size limits configured.
- SQL uses parameter binding, not string concatenation.
- Dependency vulnerabilities reviewed.
- Error responses avoid leaking internals.
- Sensitive data and tokens excluded from logs.
- Rate limiting and abuse controls considered.
- CORS and CSRF policies reviewed.
- Administrative endpoints restricted.
- Database backups and restoration tested.

---

## 16. Testing

### 16.1 Test layers

- Unit tests: test business logic with dependencies isolated.
- MVC slice tests: test controller routing, serialization, validation, and status codes.
- Repository tests: test persistence behavior.
- Integration tests: verify multiple layers together.
- End-to-end tests: test real user workflows.

### 16.2 JUnit and Mockito example

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {
    @Mock
    private UserRepository repository;

    @InjectMocks
    private UserService userService;

    @Test
    void createRejectsDuplicateEmail() {
        when(repository.existsByEmailIgnoreCase("a@example.com"))
            .thenReturn(true);

        assertThrows(IllegalArgumentException.class,
            () -> userService.create("Asha", "a@example.com"));

        verify(repository).existsByEmailIgnoreCase("a@example.com");
        verify(repository, never()).save(any());
    }
}
```

This assumes the `UserService` implementation from the JPA section and the corresponding Mockito/JUnit imports.

### 16.3 MVC tests

Use `@WebMvcTest` and `MockMvc` to test the web layer without loading the entire application. If Spring Security is enabled, configure the test authentication and CSRF setup according to the security behavior being tested.

### 16.4 Run tests

```bash
./mvnw test
```

PowerShell:

```powershell
.\mvnw.cmd test
```

Do not rely on code coverage percentage alone. Assert business outcomes, permissions, failure cases, and persistence constraints.

---

## 17. Logging and monitoring

### Logging

Use SLF4J through a logger instead of printing directly to standard output.

```java
private static final Logger log =
    LoggerFactory.getLogger(UserService.class);

log.info("User created with id={}", userId);
log.warn("User lookup failed for id={}", userId);
```

Never log passwords, bearer tokens, session IDs, or unnecessary sensitive personal data.

### Actuator

Add the Spring Boot Actuator starter. Configure only the endpoints needed.

```properties
management.endpoints.web.exposure.include=health,info,metrics
management.endpoint.health.show-details=never
```

Do not expose sensitive management endpoints publicly. Health endpoints should reveal only the information necessary for their audience.

Monitoring concepts:

- Liveness vs readiness
- Metrics and dashboards
- Distributed tracing
- Correlation/request IDs
- Error rate, latency, throughput
- Structured logs
- Alerting and incident response

---

## 18. Caching, async, and scheduling

### 18.1 Caching

Caching can reduce repeated work but requires a clear invalidation strategy.

```java
@Configuration
@EnableCaching
class CacheConfig {}

@Service
class ProductService {
    @Cacheable("products")
    public Product findById(Long id) {
        // Read from repository in a real implementation.
        return loadProduct(id);
    }

    private Product loadProduct(Long id) {
        throw new UnsupportedOperationException("Implement repository lookup");
    }
}
```

Choose a real cache provider for production where needed. Consider TTL, invalidation, stale data, cache stampedes, and multi-instance deployments.

### 18.2 Async work

`@Async` can move eligible work to a configured executor. Define bounded thread pools, handle failures, and avoid assuming that asynchronous execution automatically makes a workflow reliable.

### 18.3 Scheduling

`@Scheduled` supports periodic tasks. In multi-instance deployments, every instance may run the same scheduled task unless you coordinate it. Use a distributed lock or a dedicated job system where duplicate execution would be harmful.

---

## 19. Architecture and design patterns

### Recommended layered architecture

```text
Controller / API layer
        |
        v
Service / business layer
        |
        v
Repository / persistence layer
        |
        v
PostgreSQL
```

### Responsibilities

- **Controller:** HTTP mapping, request validation, response status.
- **Service:** business rules, transaction boundaries, orchestration.
- **Repository:** persistence operations.
- **DTO:** API contract.
- **Entity:** persistence model.
- **Configuration:** framework and integration setup.
- **Security:** authentication and authorization configuration.

### Design principles and patterns

- SOLID principles.
- Dependency inversion and constructor injection.
- Strategy pattern for interchangeable behavior.
- Factory pattern for controlled object creation.
- Builder pattern for complex object construction.
- Adapter pattern for external integrations.
- Repository pattern for persistence abstraction.
- DTO mapping to keep external API contracts independent from entities.
- Separation of concerns and single responsibility.

Avoid adding interfaces and design patterns mechanically. Introduce abstractions when they clarify responsibilities, isolate change, or improve testability.

### Monolith vs microservices

Start with a well-structured modular monolith unless there is a clear need for independently deployed services. Microservices introduce network failure, distributed tracing, deployment coordination, data ownership, and eventual-consistency complexity.

---

## 20. React integration

Assume the React app runs on `http://localhost:5173` and Spring Boot runs on `http://localhost:8080`.

### Fetch example

```javascript
async function fetchUsers() {
  const response = await fetch("http://localhost:8080/api/users");

  if (!response.ok) {
    throw new Error(`Request failed: ${response.status}`);
  }

  return response.json();
}
```

For a protected endpoint:

```javascript
async function fetchProtectedUsers(accessToken) {
  const response = await fetch("http://localhost:8080/api/users", {
    headers: {
      Authorization: `Bearer ${accessToken}`,
    },
  });

  if (!response.ok) {
    throw new Error(`Request failed: ${response.status}`);
  }

  return response.json();
}
```

The token must come from a properly designed authentication flow; do not hard-code secrets in React source code.

### Frontend integration checklist

- Configure the backend's allowed CORS origins.
- Agree on DTO field names and response/error formats.
- Handle loading, empty, success, and failure states.
- Handle `401` and `403` without assuming they mean the same thing.
- Use environment-specific API base URLs.
- Never rely on client-side route guards for backend authorization.
- Prefer a reverse proxy or same-origin deployment where appropriate.

---

## 21. Docker and local development

### 21.1 Dockerfile

Build the application JAR first. The following example assumes the generated JAR is copied to `target/app.jar`.

```dockerfile
FROM eclipse-temurin:21-jre
WORKDIR /app
COPY target/app.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "/app/app.jar"]
```

For production, pin base images deliberately, scan them, and use a maintained JRE image. A multi-stage build can compile inside Docker, but the example above expects the JAR to exist before image creation.

### 21.2 Build and run

```bash
./mvnw clean package
cp target/your-artifact.jar target/app.jar
docker build -t testflow-api:local .
docker run --rm -p 8080:8080 \
  -e DB_URL='jdbc:postgresql://host.docker.internal:5432/testflow' \
  -e DB_USERNAME='testflow_app' \
  -e DB_PASSWORD='replace-with-local-secret' \
  testflow-api:local
```

On Linux, `host.docker.internal` may require additional host configuration. Docker networking differs by platform; use a Compose service name when the database is in the same Compose network.

### 21.3 Docker Compose for local development

```yaml
services:
  db:
    image: postgres:16
    environment:
      POSTGRES_DB: testflow
      POSTGRES_USER: testflow_app
      POSTGRES_PASSWORD: local-dev-only-password
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U testflow_app -d testflow"]
      interval: 5s
      timeout: 3s
      retries: 10

volumes:
  postgres_data:
```

This is a local learning example. Do not use its sample password in production or commit real credentials. When the API is also in Compose, connect to the database using hostname `db` and the container port `5432`.

Start the database:

```bash
docker compose up -d db
docker compose logs -f db
```

Stop the services:

```bash
docker compose down
```

`docker compose down -v` also deletes the named data volume and should be used only when you intend to discard the local database.

---

## 22. Deployment

Deployment depends on the hosting platform. The following is the general process for a VM, container platform, or managed application service.

### 22.1 Production checklist

1. Build and test the application in CI.
2. Package the JAR or build a container image.
3. Provision a managed PostgreSQL database or a properly maintained database server.
4. Apply schema migrations through a controlled deployment step.
5. Configure environment variables and secrets in the platform's secret store.
6. Configure HTTPS and routing/reverse proxy.
7. Set resource limits and JVM/container memory appropriately.
8. Configure health checks and logs.
9. Restrict management endpoints.
10. Run smoke tests after deployment.
11. Monitor errors, latency, resource use, and database connections.
12. Maintain backups, restoration tests, and a rollback plan.

### 22.2 VM deployment outline

- Install a supported Java runtime or run the application as a container.
- Upload the artifact using a secure deployment pipeline.
- Configure a dedicated operating-system user.
- Set environment variables via a protected service configuration.
- Run the app using a service manager such as systemd.
- Put a TLS-enabled reverse proxy in front if required.
- Configure log rotation, monitoring, restart behavior, and firewall rules.

### 22.3 Container platform outline

- Build an immutable container image in CI.
- Push the image to a container registry.
- Deploy with secrets and configuration injected by the platform.
- Configure readiness/liveness probes, resource requests/limits, and autoscaling based on measured needs.
- Use a managed database and private networking where available.
- Roll out gradually and monitor error rates.
- Never bake production secrets into the image.

### 22.4 Database deployment concerns

- Back up before risky schema changes.
- Test migrations against realistic data.
- Prefer backward-compatible schema changes for rolling deployments.
- Keep credentials and migration permissions appropriately separated.
- Monitor connection pool usage and query latency.
- Test recovery, not just backup creation.

---

## 23. CI/CD example

Example GitHub Actions workflow for a Maven project. Adapt Java and action versions to your repository's security and maintenance policy.

```yaml
name: Java CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Set up Java
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '21'
          cache: maven

      - name: Run tests
        run: ./mvnw --batch-mode clean verify
```

For production, extend the pipeline with dependency and image scanning, artifact/image publishing, protected environments, deployment approval where appropriate, smoke tests, and rollback procedures. Pin third-party actions to trusted immutable references according to your organization's policy.

---

## 24. Performance and production readiness

### Application performance

- Measure before optimizing.
- Use pagination for large results.
- Inspect slow queries with `EXPLAIN ANALYZE` in a safe environment.
- Add indexes based on query patterns and evidence.
- Watch for N+1 queries.
- Configure connection pools based on workload and database limits.
- Avoid blocking operations on inappropriate execution threads.
- Use caching only with explicit invalidation rules.
- Set request timeouts and external-client timeouts.
- Bound executor queues and concurrent workloads.

### Reliability

- Retry only transient failures and only when safe.
- Use idempotency keys for operations where client retries could duplicate effects.
- Define transaction boundaries carefully.
- Keep health checks useful but inexpensive.
- Gracefully shut down workers and servers.
- Monitor memory, CPU, garbage collection, connection pools, and latency.
- Maintain logs, metrics, traces, and alerts.
- Test disaster recovery and database restoration.

### Code quality

- Keep controllers thin.
- Put business rules in services/domain logic.
- Use DTOs for API boundaries.
- Handle errors consistently.
- Add unit, integration, and security tests.
- Review dependencies and patch regularly.
- Document configuration and runbooks.

---

## 25. Common errors and fixes

| Problem | Possible cause | What to check |
|---|---|---|
| `java` not found | JDK missing or PATH incorrect | JDK install, `JAVA_HOME`, terminal restart |
| Unsupported class version | Build/runtime Java mismatch | Maven compiler target and runtime version |
| Port 8080 already in use | Another service is listening | Stop the process or configure `server.port` |
| Database connection refused | PostgreSQL not running or wrong host/port | Database status, JDBC URL, Docker networking |
| Authentication required unexpectedly | Spring Security default configuration | Inspect security dependencies and filter chain |
| 403 on POST | CSRF protection, authorization, or CORS-related assumptions | Inspect server logs, CSRF setup, and security rules |
| 404 endpoint | Wrong path, HTTP method, or component scanning | Mapping annotations and application logs |
| 400 request error | JSON shape or validation failed | DTO types, required fields, response body |
| Duplicate key violation | Existing data or concurrent insert | Database constraints and conflict handling |
| Lazy initialization error | Access to lazy relation outside suitable context | Query design, DTO mapping, fetch plan |
| Slow API | Slow SQL, N+1 queries, blocking work, remote dependency | Metrics, traces, query plans |
| Tests fail only with security | Security filters require authentication/CSRF | Configure tests for intended security behavior |

Always diagnose from the full error message and logs; the same symptom can have different causes.

---

## 26. Interview questions and short answers

### Core Spring

**1. What is Spring Boot?**  
A framework built on Spring that simplifies setup through auto-configuration, starters, and embedded-server support.

**2. What is dependency injection?**  
A technique where a container supplies an object's dependencies rather than the object constructing them itself.

**3. What is a Spring bean?**  
An object managed by the Spring container.

**4. `@Component` vs `@Bean`?**  
`@Component` is discovered through component scanning; `@Bean` explicitly declares the object returned by a configuration method.

**5. `@Controller` vs `@RestController`?**  
`@RestController` combines controller semantics with response-body behavior by default; `@Controller` is commonly used for MVC view handling.

### REST and validation

**6. What is REST?**  
An architectural style that models resources and uses a uniform interface, commonly HTTP methods and status codes.

**7. Why use DTOs?**  
To control API contracts, validate input, and avoid coupling external responses directly to persistence entities.

**8. Why use global exception handling?**  
To provide consistent error responses and centralize mapping of known failures to HTTP status codes.

### JPA and databases

**9. JPA vs Hibernate?**  
JPA is the persistence specification; Hibernate is a common implementation.

**10. What is `@Transactional`?**  
It defines transaction behavior around a method/class under the configured transaction-management mechanism.

**11. What is lazy loading?**  
Related data is fetched when accessed, subject to persistence-context and provider behavior.

**12. What is the N+1 problem?**  
A query loads a set of rows, then extra queries fetch related data for each row or group.

**13. Why are database constraints important?**  
They protect data integrity even when requests race or other application paths bypass business-level checks.

### Security

**14. Authentication vs authorization?**  
Authentication verifies identity; authorization checks permitted actions.

**15. What is JWT?**  
A token format that can carry signed claims; a resource server must validate it before trusting the claims.

**16. What is CORS?**  
A browser-enforced policy governing cross-origin access to responses; it does not replace server authorization.

**17. What is CSRF?**  
An attack that tricks a browser into sending an unwanted authenticated request, especially relevant when credentials are automatically attached.

### Testing and deployment

**18. Unit test vs integration test?**  
A unit test isolates a small unit; an integration test verifies interactions between components or systems.

**19. What is Actuator?**  
Spring Boot's production-oriented facilities for health, metrics, and operational endpoints.

**20. How do you deploy Spring Boot?**  
Build and test an artifact/image, configure external secrets and database connectivity, deploy with health checks and monitoring, then verify and retain a rollback plan.

**21. How do you secure production APIs?**  
Use HTTPS, validated authentication, server-side authorization, input validation, least-privilege access, safe secret management, and security tests.

**22. How do you improve API performance?**  
Measure latency, inspect query plans, optimize data access, paginate, eliminate unnecessary work, and apply caching only when it suits the data's consistency requirements.

---

## 27. Practice roadmap

### Week 1 — Setup and fundamentals

- Install JDK, IDE, Git, Maven wrapper, and PostgreSQL.
- Generate a Spring Boot application.
- Learn beans, IoC, DI, and configuration.
- Build two GET endpoints and one POST endpoint.
- Learn HTTP status codes and DTOs.

### Week 2 — Database and JPA

- Create the database and app role.
- Configure PostgreSQL.
- Build entity, repository, service, and controller layers.
- Learn relationships, pagination, validation, and transactions.
- Add Flyway/Liquibase migrations.

### Week 3 — Security and testing

- Learn authentication and authorization.
- Configure endpoint rules and password hashing.
- Learn OAuth2 Resource Server/JWT concepts.
- Add global exception handling.
- Write unit, MVC, repository, and integration tests.

### Week 4 — Production basics

- Add logging and Actuator.
- Learn CORS, CSRF, secrets, and deployment configuration.
- Build a Docker image and run PostgreSQL locally with Compose.
- Create CI that runs tests.
- Deploy to a suitable platform and test the live API.
- Practice explaining architectural trade-offs.

### Capstone project: TESTFlow

Build a task/workflow management backend:

- Users and roles
- Projects and tasks
- Task assignment and status transitions
- Comments and audit history
- Pagination, filtering, and sorting
- PostgreSQL persistence
- REST APIs consumed by React
- Authentication and role/resource-level authorization
- Unit and integration tests
- Docker and CI/CD
- Logging, health checks, and deployment documentation

Suggested core tables:

```text
users
roles
user_roles
projects
project_members
tasks
comments
audit_events
```

Model constraints and relationships deliberately. For production-grade audit trails, define who can see and modify records and how history is retained.

---

## 28. Official references

Use official documentation when checking version-specific configuration and behavior:

- Spring Boot documentation: https://docs.spring.io/spring-boot/
- Spring Framework documentation: https://docs.spring.io/spring-framework/reference/
- Spring Security documentation: https://docs.spring.io/spring-security/reference/
- Spring Data JPA documentation: https://docs.spring.io/spring-data/jpa/reference/
- Spring Initializr: https://start.spring.io/
- PostgreSQL documentation: https://www.postgresql.org/docs/
- Maven documentation: https://maven.apache.org/guides/
- Docker documentation: https://docs.docker.com/
- Java documentation: https://docs.oracle.com/en/java/

---

## Final checklist

- [ ] I can install the JDK and run a generated Spring Boot project.
- [ ] I understand IoC, dependency injection, and beans.
- [ ] I can create REST endpoints with DTOs and validation.
- [ ] I can implement global exception handling.
- [ ] I can connect Spring Data JPA to PostgreSQL.
- [ ] I understand transactions, migrations, and query performance.
- [ ] I can explain Spring Security, JWT validation, CORS, and CSRF.
- [ ] I can write unit and integration tests.
- [ ] I can build and run a Docker image.
- [ ] I can configure secrets, health checks, logs, and deployment.
- [ ] I can explain the architecture and trade-offs of TESTFlow.

**Study rule:** For each topic, be able to define it, explain why it is needed, write a small example, describe common mistakes, and discuss its production trade-offs.
"""
out.write_text(textwrap.dedent(content).strip() + "\n", encoding="utf-8")
print(f"Created: {out}")
print(f"Size: {out.stat().st_size:,} bytes")
