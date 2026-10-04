# Doctor-Patient Management System

A healthcare management platform built as a set of Spring Boot microservices with a React and TypeScript front end. It covers user authentication, patient and doctor profiles, appointments, prescriptions, billing and notifications, and ships with Docker, Kubernetes and CI/CD configuration.

## Architecture

Requests from the React client pass through an API gateway that validates JWTs and routes to the individual services. Every service registers with a Eureka discovery server and loads shared configuration from a Spring Cloud Config server. Each business service owns its own PostgreSQL database.

```text
React client
     |
API Gateway  (JWT validation, routing, CORS)
     |
 +---+---+---------+-------------+---------------+---------+--------------+
 |       |         |             |               |         |              |
Auth  Patient    Doctor     Appointment    Prescription  Billing   Notification

 All services register with Eureka and pull configuration from the Config Server
```

## Services

| Module | Location | Build tool |
| --- | --- | --- |
| API gateway | `backend/api-gateway` | Maven |
| Authentication | `backend/auth-service` | Maven |
| Patients | `backend/patient-service` | Maven |
| Doctors | `backend/doctor-service` | Maven |
| Appointments | `backend/appointment-service` | Maven |
| Prescriptions | `backend/prescription-service` | Maven |
| Billing | `backend/billing-service` | Maven |
| Notifications | `backend/notification-service` | Maven |
| Config server | `backend/config-server` | Maven |
| Discovery (Eureka) | `backend/eureka-server` | Gradle |
| Web client | `frontend-react` | npm and Vite |

## Tech Stack

| Area | Technology |
| --- | --- |
| Backend | Java 21, Spring Boot 3.2, Spring Cloud (Gateway, Config, Eureka), Spring Security with JWT |
| API docs | springdoc OpenAPI (Swagger UI) |
| Database | PostgreSQL, one database per service |
| Frontend | React 18, TypeScript, Vite, Tailwind CSS, React Router, Axios |
| Packaging | Docker, Docker Compose |
| Orchestration | Kubernetes manifests (namespace, config maps, deployments, stateful sets, ingress, autoscaling) |
| Monitoring | Prometheus and Grafana configuration in `docker` |
| CI/CD | GitHub Actions workflow in `.github/workflows/ci-cd.yml` |

## Front-end Pages

- Sign in and registration
- Patient dashboard and profile creation
- Doctor dashboard and profile creation
- Appointment booking and appointment list
- Prescriptions
- Administrator area: dashboard, user management, departments and appointments

## Getting Started

### Prerequisites

- Java 21 and Maven 3.9 or later
- Node.js 20 or later
- PostgreSQL 16 (or Docker)
- Docker Desktop with Compose, if you want to run the stack in containers

### Configuration

Copy `.env.example` to `.env` and fill in the database connection details and a long random `JWT_SECRET`. Create the PostgreSQL databases referenced in the file before starting the services. The `.env` file is ignored by Git.

### Run with Docker Compose

```bash
git clone https://github.com/mosesaderemi/doctor-patient-system.git
cd doctor-patient-system
copy .env.example .env
docker compose up -d --build
```

The compose file builds and starts the config server, Eureka server, authentication, patient, doctor, appointment and prescription services, and the API gateway. Check service health with `docker compose ps`. The billing and notification services can be run from their folders with Maven.

### Run without Docker

Start the services in this order, each in its own terminal:

```bash
cd backend/eureka-server && ./gradlew bootRun
cd backend/config-server && mvn spring-boot:run
cd backend/auth-service && mvn spring-boot:run
# repeat for the patient, doctor, appointment, prescription, billing and notification services
cd backend/api-gateway && mvn spring-boot:run
```

Then start the web client:

```bash
cd frontend-react
npm install
npm run dev
```

The client is served on `http://localhost:3000`.

### Kubernetes

Manifests are in `kubernetes`. Copy `kubernetes/secrets/secrets.example.yaml` to `secrets.yaml`, replace every `REPLACE_ME` value with real credentials, and apply the manifests in this order: namespace, secrets, config maps, stateful sets, deployments, ingress, autoscaling. The real `secrets.yaml` is ignored by Git.

## CI/CD

The GitHub Actions workflow runs Maven tests for each backend service, then type-checks and lints the front end.

## Security Notes

- Never commit real secrets. Use `.env` locally and Kubernetes secrets in clusters.
- Healthcare data is sensitive. Review authentication, authorisation and data protection requirements before using any part of this project with real patient information.

## Default Administrator

The authentication service migration (`V1__init_auth_schema.sql`) creates an administrator account named `admin`. Change its password immediately after the first start.

## Author

Aderemi Moses Timileyin
GitHub: [@mosesaderemi](https://github.com/mosesaderemi)
LinkedIn: [linkedin.com/in/mosesaderemi-6a70a5405](https://www.linkedin.com/in/mosesaderemi-6a70a5405)

## License

Released under the MIT License. See the `LICENSE` file for details.
