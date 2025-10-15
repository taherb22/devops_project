# CI/CD Pipeline for a Spring Boot Application

This repository contains the configuration and source code for a complete CI/CD pipeline designed to automatically build, test, and deploy a Java Spring Boot application.

The primary goal of this project is to demonstrate a modern DevOps workflow using industry-standard tools to achieve automation and ensure code quality.

## Pipeline Architecture

The pipeline follows a logical flow from code commit to deployment and monitoring:

`Git Commit` -> `Jenkins (Webhook Trigger)` -> `Maven Build & Test` -> `SonarQube Analysis` -> `Nexus Artifact Upload` -> `Docker Image Build & Push` -> `Deploy` -> `Monitoring with Prometheus & Grafana`

## The Toolchain

Each tool in this pipeline plays a specific and critical role:

*   **Java / Spring Boot**: The sample application that the pipeline is built for.
*   **Jenkins**: The automation server and orchestrator of the entire CI/CD process. The pipeline is defined as code in the `Jenkinsfile`.
*   **Maven**: The build tool used to compile the Java code, run unit tests, and package the application into a `.jar` file.
*   **JUnit**: The framework used for writing and running unit tests.
*   **SonarQube**: The static code analysis tool that scans the code for bugs, vulnerabilities, and code smells, acting as a quality gate.
*   **Nexus Repository**: The artifact manager where the compiled `.jar` file is stored and versioned.
*   **Docker**: The containerization platform used to package the Spring Boot application and its dependencies into a portable Docker image.
*   **Prometheus**: The monitoring system that scrapes metrics from the running application's `/actuator/prometheus` endpoint.
*   **Grafana**: The visualization tool that connects to Prometheus to display application metrics on dashboards.

## How the Pipeline Works

The `Jenkinsfile` in this repository defines the following stages:

1.  **Checkout**: Clones the source code from the Git repository.
2.  **Build**: Compiles the code and runs all unit tests using Maven (`mvn clean install`).
3.  **Code Quality Scan**: Pushes the code to SonarQube for analysis and waits for the quality gate to pass.
4.  **Publish to Nexus**: If the quality gate passes, it publishes the packaged `.jar` artifact to our Nexus repository.
5.  **Build Docker Image**: Builds a Docker image using the `Dockerfile` in this repository.
6.  **Push Docker Image**: Pushes the newly created image to a Docker registry (like Docker Hub or a private registry).
7.  **Deploy**: (Placeholder Stage) This stage would contain the script to deploy the container to a target environment (e.g., using `kubectl apply` for Kubernetes or `docker run` on a server).

## Setting Up the Project

This project assumes you have the core services running and accessible. For local testing, it's recommended to run each service in Docker.

### Prerequisites

*   A running **Jenkins** instance with the required plugins (Blue Ocean, Docker, SonarScanner).
*   A running **SonarQube** server.
*   A running **Nexus Repository** manager.
*   **Docker** installed and running on the Jenkins agent.

### Configuration

1.  **Jenkins Credentials**:
    *   In Jenkins, configure the necessary credentials for SonarQube, Nexus, and your Docker registry in `Manage Jenkins > Credentials`.
    *   These credential IDs must match the ones used in the `Jenkinsfile`.

2.  **Webhook**:
    *   Configure a webhook in your Git repository to point to your Jenkins server's `/github-webhook/` endpoint. This will trigger the pipeline automatically on every push.

3.  **SonarQube Quality Gate**:
    *   Ensure a quality gate is configured in SonarQube. The pipeline will check against this gate.

4.  **Run the Pipeline**:
    *   In Jenkins, create a new "Pipeline" job and point it to this repository.
    *   The pipeline will be triggered on the first run and subsequently by any new commits.
