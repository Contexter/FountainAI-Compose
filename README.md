Here's the updated **FountainAI Development and Deployment Guide** with the **contact information** removed from the **OpenAPI specification** examples, leaving only the `x-repo-url`.

---

# **FountainAI Development and Deployment Guide**

## Introduction

The FountainAI project is a collection of independently developed microservices, each encapsulated in its own GitHub repository and designed as a FastAPI application. This guide provides a **centralized approach to deploying, managing, and testing** the FountainAI ecosystem using Docker Compose.

The **FountainAI-Compose** repository is dedicated to managing the full-stack deployment of all FountainAI services, allowing each service to function as an isolated, containerized application that integrates seamlessly in the FountainAI ecosystem.

### Objectives of this Guide:
- Enable **modular, specification-driven local development** for each FountainAI microservice.
- Provide a **centralized Docker Compose setup** for integration testing, local full-stack development, and production deployment.
- Outline best practices for transitioning from **local development** in individual repositories to **full-stack integration** within FountainAI-Compose.
- **Centralize key information** in the OpenAPI specifications, making them the **single source of truth** for each microservice.

---

## FountainAI Microservices Overview

The FountainAI system consists of the following microservices, each hosted in its own GitHub repository:

1. [**Ensemble Service**](https://github.com/Contexter/Ensemble-Service) - The main, public-facing service that routes requests to other internal FountainAI services.
2. [**Story Factory Service**](https://github.com/Contexter/Story-Factory-Service) - Manages the generation and orchestration of story components.
3. [**Spoken Word Service**](https://github.com/Contexter/Spoken-Word-Service) - Handles processing and generation of spoken lines.
4. [**Core Script Management Service**](https://github.com/Contexter/Core-Script-Managment-Service) - Manages scripts, scenes, and core narrative structures.
5. [**Session and Context Service**](https://github.com/Contexter/Session-And-Context-Service) - Manages user sessions and contextual information.
6. [**Character Service**](https://github.com/Contexter/Character-Service) - Manages character data and attributes within the FountainAI environment.
7. [**Performer Service**](https://github.com/Contexter/Performer-Service) - Manages performer profiles and assignments to characters.
8. [**Central Sequence Service**](https://github.com/Contexter/Central-Sequence-Service) - Handles sequencing and chronological data across the FountainAI services.
9. [**Paraphrase Service**](https://github.com/Contexter/Paraphrase-Service) - Manages paraphrasing and text alternatives.
10. [**Action Service**](https://github.com/Contexter/Action-Service) - Manages actions associated with characters and narrative events.

### Key Concepts

Each microservice is developed, tested, and deployed independently. The **Ensemble Service** is the only public-facing component, responsible for routing requests to internal services. All other services operate within an internal Docker network managed by Docker Compose.

---

## 1. Local Development of Individual Services

### Service-Specific Repositories

Each service is developed in its own GitHub repository, focusing exclusively on **Dockerized local development** without introducing inter-service dependencies or Docker Compose configurations. This allows each service to:
- Be **developed and tested independently**.
- Use a **self-contained `Dockerfile`** for containerization.
- Avoid any dependency on other FountainAI services for local development.

### Local Development Workflow for a Microservice

1. **Clone the Microservice Repository**:
   Clone the repository for the microservice you wish to work on, for example:
   ```bash
   git clone https://github.com/Contexter/FountainAI-Action-Service.git
   cd FountainAI-Action-Service
   ```

2. **Build and Run the Dockerized Service**:
   Each service includes a `Dockerfile` that handles containerization for local development. Use the following commands to build and run the service:
   ```bash
   docker build -t fountainai-action-service .
   docker run -p 8000:8000 fountainai-action-service
   ```
   This setup runs the service on `localhost:8000`, enabling you to test endpoints independently.

3. **Testing and Validation**:
   - Use tools like **Postman** or **curl** to interact with the API endpoints.
   - Run any included tests (e.g., using pytest) to validate functionality in isolation.
   - The service can be developed, refined, and tested independently of other services until ready for integration.

4. **Script-Based Automation (Optional)**:
   Some services may include modular scripts (e.g., `generate_dockerfile.py`, `generate_models.py`, `generate_routes.py`) that automate setup based on OpenAPI specifications. These scripts streamline the initial setup and ensure consistency with the API specification.

### Note:
No `docker-compose.yml` file should be included in individual service repositories. All inter-service dependencies and network configurations are managed in this centralized **FountainAI-Compose** repository.

---

## 2. Centralized Integration with Docker Compose

The **FountainAI-Compose** repository contains the **centralized Docker Compose configuration** (`docker-compose.yml`) for deploying and testing all services together. This setup facilitates:
- **Full-Stack Development**: Run all services in a unified environment to test inter-service interactions.
- **Production-Ready Deployment**: Centralize environment variables, manage network configurations, and control service dependencies.
- **Consistent Service Discovery**: Use Docker's internal networking, allowing services to communicate with each other via service names (e.g., `http://action-service:8001`).

### Repository Structure

The **FountainAI-Compose** repository is organized as follows:

```plaintext
FountainAI-Compose/
├── README.md
└── docker-compose.yml
```

- **docker-compose.yml**: Defines each FountainAI service, specifying its Docker image or build context and relevant configurations for centralized deployment.
- **README.md**: The current guide, providing setup instructions for deploying FountainAI in a unified environment.

---

## 3. Centralized Docker Compose Configuration

The `docker-compose.yml` file in the **FountainAI-Compose** repository references each service by its GitHub repository, ensuring the latest code is pulled or by using pre-built images for production environments.

### Sample `docker-compose.yml`

Below is an example `docker-compose.yml` file, where each service is referenced by its repository URL:

```yaml
version: '3.8'

services:
  # Public-facing Ensemble Service
  ensemble-service:
    build:
      context: https://github.com/Contexter/Ensemble-Service.git
    ports:
      - "8000:8000"  # Expose Ensemble Service on port 8000
    environment:
      - INTERNAL_ACTION_SERVICE_URL=http://action-service:8001
      - INTERNAL_CHARACTER_SERVICE_URL=http://character-service:8002
      - INTERNAL_CENTRAL_SEQUENCE_SERVICE_URL=http://central-sequence-service:8003
      - INTERNAL_CORE_SCRIPT_SERVICE_URL=http://core-script-service:8004
      - INTERNAL_PARAPHRASE_SERVICE_URL=http://paraphrase-service:8005
      - INTERNAL_PERFORMER_SERVICE_URL=http://performer-service:8006
      - INTERNAL_SESSION_CONTEXT_SERVICE_URL=http://session-context-service:8007
      - INTERNAL_SPOKEN_WORD_SERVICE_URL=http://spoken-word-service:8008
      - INTERNAL_STORY_FACTORY_SERVICE_URL=http://story-factory-service:8009
    depends_on:
      - action-service
      - character-service
      - central-sequence-service
      - core-script-service
      - paraphrase-service
      - performer-service
      - session-context-service
      - spoken-word-service
      - story-factory-service

  # Internal Services
  action-service:
    build:
      context: https://github.com/Contexter/Action-Service.git
    expose:
      - "8001"

  character-service:
    build:
      context: https://github.com/Contexter/Character-Service.git
    expose:
      - "8002"

  central-sequence-service:
    build:
      context: https://github.com/Contexter/Central-Sequence-Service.git
    expose:
      - "8003"

  core-script-service:
    build:
      context: https://github.com/Contexter/Core-Script-Managment-Service.git
    expose:
      - "8004"

  paraphrase-service:
    build:
      context: https://github.com/Contexter/Paraphrase-Service.git
    expose:
      - "8005"

  performer-service:
    build:
      context: https://github.com/Contexter/Performer-Service.git
    expose:
      - "8006"

  session-context-service:
    build:
      context: https://github.com/Contexter/Session-And-Context-Service.git
    expose:
      - "8007"

  spoken-word-service:
    build:
      context: https://github.com/Contexter/Spoken-Word-Service.git
    expose:
      - "8008"

  story-factory-service:
    build:
      context: https://github.com/Contexter/Story-Factory-Service.git
    expose:
      - "8009"
```

---

## 4. OpenAPI Specifications as the Single Source of Truth

To ensure consistency and transparency across all FountainAI services, each microservice's **OpenAPI specification** includes metadata about the service’s repository. This makes the OpenAPI specification the **single source of truth** for

 the service.

### Repository URL in OpenAPI Spec

The **OpenAPI specification** of each service includes a direct link to the service’s GitHub repository using the `x-repo-url` custom field.

#### Example OpenAPI Metadata for a Service

```yaml
openapi: 3.1.0
info:
  title: FountainAI Action Service
  description: >
    The FountainAI Action Service manages actions associated with characters and spoken words.
  version: 1.0.0
  x-repo-url: https://github.com/Contexter/Action-Service
```

By centralizing repository information in the OpenAPI spec:
- **Developers and automation tools** can easily access the latest codebase.
- The OpenAPI spec acts as a single source of truth, ensuring consistency across all resources.

### Standardizing OpenAPI Metadata Across Services

All FountainAI microservices follow this format for their OpenAPI specifications, ensuring uniformity. This makes it easy to navigate between code and documentation directly from the OpenAPI spec.

---

## 5. Deployment Workflow

The FountainAI deployment process includes the following steps:

### Step 1: Clone the FountainAI-Compose Repository

Clone this repository, which contains the centralized Docker Compose setup:

```bash
git clone https://github.com/Contexter/FountainAI-Compose.git
cd FountainAI-Compose
```

### Step 2: Build and Start the Stack

Run the following command to build and start all services as specified in `docker-compose.yml`:

```bash
docker-compose up --build -d
```

This command will:
- Build and run all services defined in `docker-compose.yml`.
- Set up the Docker network, enabling internal communication between services.
- Start the public-facing Ensemble Service on `localhost:8000`.

### Step 3: Access and Test the Application

- **Access the Ensemble Service** at [http://localhost:8000](http://localhost:8000) to verify that it routes requests to internal services.
- **Run tests** on the centralized setup as needed to validate full-stack functionality.

### Step 4: Shut Down the Environment

When testing or deployment is complete, you can stop the Docker containers with:

```bash
docker-compose down
```

### Transitioning to Production

For production, replace `build` directives with `image` directives, referencing Docker images from a container registry (e.g., Docker Hub) rather than building directly from GitHub repositories. This improves stability and speed during deployment.

---

## Summary

The FountainAI-Compose repository serves as the **centralized integration point** for all FountainAI services, supporting a seamless transition from local, independent development of each microservice to full-stack deployment. By centralizing the Docker Compose setup here, we enable a modular, scalable approach where each service repository remains focused on self-contained Dockerized development, while this repository manages inter-service dependencies, configuration, and deployment.

This guide ensures that FountainAI is:
- **Modular**: Each service can be developed and tested independently.
- **Unified**: All services are integrated for full-stack development and testing in FountainAI-Compose.
- **Production-Ready**: Supports transition to production with pre-built Docker images and a single, streamlined deployment configuration.
- **OpenAPI-Driven**: Each service’s OpenAPI specification acts as the **single source of truth**, centralizing repository links for consistency across the ecosystem.
