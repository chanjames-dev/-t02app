# Project 2: Containerize and Run Locally

## Course Information
- Course: Containerization Project
- Project: Project 2 - Containerize and Run Locally

## Overview
This project demonstrates a multi-stage Docker build, a Docker Compose
application with a Redis cache service, and an AWS SAM function that
can be invoked locally.

## Prerequisites
- Docker Desktop with WSL 2 integration
- Docker Compose
- AWS SAM CLI
- Git

## 1. Build and start the application

From the project root, run:

    docker compose up -d --build

## 2. Verify the services

Check service status:

    docker compose ps

Check the website:

    curl -s -o /dev/null -w "%{http_code}\n" http://localhost:8080

Expected HTTP status: 200

Check Redis:

    docker compose exec cache redis-cli ping

Expected response: PONG

## 3. Invoke the local Lambda function

From the project root:

    cd local-fn
    sam local invoke HelloWorldFunction

Expected response: statusCode 200 and a body containing "hello world".

Return to the project root:

    cd ..

## 4. Stop the services

    docker compose down

## 5. Compare Docker image sizes

Build the single-stage image:

    docker build -f Dockerfile.single -t demo:single .

Build the multi-stage image:

    docker build -t demo:multi .

Compare the images:

    docker images demo

## Architecture

- The multi-stage Dockerfile uses Node.js to build the website and
  Nginx Alpine to serve the generated files.
- Docker Compose runs the web service and Redis as separate services.
- Redis has a health check, and the web service waits for Redis to become
  healthy before starting.
- The local-fn directory contains an AWS SAM function that runs locally.
  An AWS account is not required for local invocation.
