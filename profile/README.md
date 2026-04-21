# 🗂️ FilePilot

FilePilot is a full-stack **document version control system** for the web. It allows users to upload, create, edit, and manage versioned documents through a clean browser-based interface. The project follows a standard client-server architecture and is split into two repositories:

- **[filepilot-backend](https://github.com/filepilot/filepilot-backend)** — Spring Boot REST API (Java)
- **[filepilot-frontend](https://github.com/filepilot/filepilot-frontend)** — React + Vite UI (JavaScript)

---

# Getting Started

For a quick overview of what FilePilot offers and how to create your first document, see the [FilePilot Wiki](https://github.com/filepilot/filepilot-backend/wiki).

---

# Architecture

```
Browser (React + Vite)
        │
        │  HTTP / REST  +  Authorization: Bearer <JWT>
        ▼
Spring Boot API  ──────►  PostgreSQL
```

- The **frontend** runs entirely in the browser and communicates with the backend over REST.
- The **backend** handles all business logic, authentication, and persistence.
- **PostgreSQL** stores documents, version history, and user accounts.
- **JWT tokens** authenticate every request after login — no sessions, fully stateless.

---

# Components

## filepilot-backend

The server-side REST API. Built with Spring Boot 3.5 and Java 21.

**Responsibilities:**
- User registration and JWT-based authentication
- Document CRUD operations
- Version history tracking and retrieval
- Request validation and error handling

The complete REST API definition can be browsed via the Swagger UI once the application is running at `/swagger-ui.html`.

## filepilot-frontend

The browser-based user interface. Built with React and Vite.

**Responsibilities:**
- Login / registration screens
- Document list and editor views
- Version history browser
- All communication with the backend via a dedicated services layer

---

# Building

## Build Tools

| Component | Language | Build Tool | Runtime |
|-----------|----------|------------|---------|
| filepilot-backend | Java 21 | Maven 3.9+ | JVM |
| filepilot-frontend | JavaScript | Vite / npm | Node.js 18+ |

Make sure your `JAVA_HOME` points to a Java 21 JDK before building the backend.

## Backend — Compile and Package

Run the following from the root of `filepilot-backend`:

```bash
$ mvn clean install
```

The deployable artifact is a JAR file located at:

```
target/filepilot-backend-<version>.jar
```

## Frontend — Install and Build

Run the following from the root of `filepilot-frontend`:

```bash
$ npm install
$ npm run build
```

The production-ready assets are output to `dist/`.

---

# Running Locally

## Prerequisites

- Java 21
- Maven 3.9+
- Node.js 18+
- A running PostgreSQL instance

## 1. Set Up the Database

Create a PostgreSQL database:

```sql
CREATE DATABASE filepilot;
```

## 2. Configure the Backend

Edit `src/main/resources/application.properties` in `filepilot-backend`:

```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/filepilot
spring.datasource.username=your_user
spring.datasource.password=your_password
spring.jpa.hibernate.ddl-auto=update

# JWT secret — use a long random string in production
app.jwt.secret=your_jwt_secret_key
app.jwt.expiration-ms=86400000
```

> **Important:** Never commit real credentials. Use environment variables or a secrets manager in production.

## 3. Start the Backend

```bash
$ cd filepilot-backend
$ ./mvnw spring-boot:run
```

The API will be available at `http://localhost:8080`.

## 4. Configure and Start the Frontend

The API base URL is controlled via environment files:

```env
# filepilot-frontend/.env.development
VITE_API_BASE_URL=http://localhost:8080
```

Then start the dev server:

```bash
$ cd filepilot-frontend
$ npm install
$ npm run dev
```

The application opens at `http://localhost:5173`.

---

# REST API

> All endpoints except `/api/auth/**` require the header:
> `Authorization: Bearer <token>`

### Authentication

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/auth/register` | Register a new user |
| `POST` | `/api/auth/login` | Authenticate and receive a JWT |

### Documents

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/documents` | List all documents for the current user |
| `POST` | `/api/documents` | Create or upload a new document |
| `GET` | `/api/documents/{id}` | Retrieve a document by ID |
| `PUT` | `/api/documents/{id}` | Update a document / save a new version |
| `DELETE` | `/api/documents/{id}` | Delete a document |

### Version History

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/documents/{id}/versions` | List all versions of a document |
| `GET` | `/api/documents/{id}/versions/{vId}` | Retrieve a specific version |

### Health

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/actuator/health` | Spring Boot Actuator health check |

---

# Deploying to Production

FilePilot's backend is packaged as a standard Spring Boot JAR and can be deployed to any environment that supports Java 21.

## Configuration via Environment Variables

For production deployments, override `application.properties` settings with environment variables:

| Variable | Description |
|----------|-------------|
| `SPRING_DATASOURCE_URL` | JDBC connection string |
| `SPRING_DATASOURCE_USERNAME` | Database user |
| `SPRING_DATASOURCE_PASSWORD` | Database password |
| `APP_JWT_SECRET` | JWT signing secret (keep this private) |

## Running the JAR

```bash
$ java -jar target/filepilot-backend-<version>.jar
```

## Frontend

After running `npm run build`, deploy the `dist/` folder to any static hosting service (Nginx, AWS S3 + CloudFront, Netlify, Vercel, etc.). Make sure `VITE_API_BASE_URL` is set to your production API URL at build time:

```bash
$ VITE_API_BASE_URL=https://api.yourdomain.com npm run build
```

---

# How to Contribute

- [Did you find a bug?](CONTRIBUTING.md#did-you-find-a-bug)
- [Do you have a question or need support?](CONTRIBUTING.md#do-you-have-a-question-or-need-support)
- [How to develop, test and contribute to FilePilot](CONTRIBUTING.md#do-you-want-to-contribute-to-the-code-base)

In short:

1. Fork the relevant repository (`filepilot-backend` or `filepilot-frontend`).
2. Create a feature branch from `main`.
3. Make your changes and add or update tests.
4. Open a pull request against `main`.

---

# Further Reading

- [Spring Boot Reference Documentation](https://docs.spring.io/spring-boot/docs/current/reference/html/)
- [Vite Documentation](https://vitejs.dev/guide/)
- [React Documentation](https://react.dev/)
- [JWT Introduction](https://jwt.io/introduction/)

---

# License

Copyright (c) 2026 FilePilot Contributors. All rights reserved.
This project is licensed under the [Apache License, Version 2.0](LICENSE).
