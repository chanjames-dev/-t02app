# Project 2 - Design Rationale

## 1. Multi-stage Docker build

The main Dockerfile uses Node.js 24 as its build stage. It installs
dependencies with npm ci and generates the website build output.

The second stage uses nginx:alpine to serve the generated website.
The Node.js build environment and development dependencies are not
copied into the final runtime image.

This separation reduces the final image size and keeps the runtime
environment focused on serving the website.

Reference:
https://docs.docker.com/build/building/multi-stage/

## 2. Single-stage comparison

Dockerfile.single provides a comparison using Node.js 24 for both
building and serving the website.

After building both images, the actual image sizes are recorded using:

    docker images demo

The measured sizes are included in the project verification results.
The multi-stage image should be smaller because it does not include
the complete Node.js build environment in its final runtime image.

## 3. Docker Compose and Redis

The Compose configuration defines two services:

- web: Builds the application using the multi-stage Dockerfile and
  publishes container port 80 on host port 8080.
- cache: Uses the redis:7-alpine public image.

Redis has a health check using redis-cli ping. The web service uses
depends_on with the service_healthy condition so it starts after Redis
passes its health check.

Reference:
https://docs.docker.com/compose/how-tos/startup-order/

## 4. Local AWS SAM function

The local-fn directory contains a Python AWS SAM Hello World function.
It can be invoked using sam local invoke without deploying it to AWS.

Reference:
https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/install-sam-cli.html

## 5. Configuration validation

Docker Compose configuration can be inspected with:

    docker compose config

This helps validate the Compose configuration before starting services.

Reference:
https://docs.docker.com/reference/cli/docker/compose/config/
