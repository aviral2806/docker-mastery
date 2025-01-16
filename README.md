# Spring Boot Dockerized Application with GitHub Actions CI/CD

This project demonstrates a containerized Spring Boot application. The application is packaged using Docker and automatically built and pushed to Docker Hub on every push to the `main` branch. The entire process is managed through a GitHub Actions workflow.

---

## Features

- A Spring Boot application containerized using Docker.
- Continuous Integration and Deployment using GitHub Actions.
- Automatic tagging of Docker images with the latest commit hash.
- Docker images pushed to Docker Hub.
- Multi stage build used for lightweight Docker image

---

## Technologies Used

- **Spring Boot**: Backend framework for Java.
- **Docker**: Containerization platform.
- **GitHub Actions**: CI/CD pipeline.
- **Docker Hub**: Container registry.

---

## Dockerfile

The following `Dockerfile` is used to build and containerize the Spring Boot application:

```dockerfile
# Build Stage
FROM maven:3.9.9-eclipse-temurin-21 AS build
COPY . /app
WORKDIR /app
RUN mvn clean package

# Runtime Stage
FROM eclipse-temurin:21-jre-alpine
COPY --from=build /app/target/dockermastery-0.0.1-SNAPSHOT.jar /app/todoapp.jar
WORKDIR /app

EXPOSE 8080
ENTRYPOINT ["java","-jar", "todoapp.jar"]
```

## Workflow File (`.github/workflows/cd.yaml`)

GitHub Actions workflow for building and publishing Docker images

```yaml
name: Build and publish docker images

on:
  workflow_dispatch:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3
      - name: Login to docker hub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      - name: Extract git hash to tag image
        run: echo "commit_hash=$(git rev-parse --short HEAD)" >> $GITHUB_ENV
      - name: Build and push docker image
        uses: docker/build-push-action@v6
        with:
          push: true
          tags: aviral2806/todoapp:${{ env.commit_hash }}
```

## How to Use

i) Clone your repository

```bash
git clone https://github.com/<your-username>/<your-repo>.git
```

ii) Configure your secrets in GitHub:

`DOCKERHUB_USERNAME`: Your Docker Hub username.

`DOCKERHUB_PASSWORD`: Your Docker Hub password.

iii) Push changes to the main branch to trigger the workflow.

- The Docker image will be built, tagged, and pushed to Docker Hub. The image will be available at:

```bash
<your-username>/<your-app-name>:<commit_hash>
```
