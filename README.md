# Jenkins Alpine Hello-World
This repository contains the necessary configuration and code to deploy an alpine-based docker application on Heroku using Jenkins pipeline.

[![Build Status](https://a369-82-66-124-175.ngrok-free.app/buildStatus/icon?job=deploiement)](http://192.168.56.11:8080/job/deploiement/)

## Prerequisites

1. Git installed on your local machine.
2. An Heroku account.
2. Jenkins Setup:
    - Environment variables
    - Secrets
    - Plugins
3. GitHub Setup:
    - GitHub repository created
    - Webhook configured to your Jenkins server

## Usage

1. Triggering the Pipeline:

- The pipeline will be triggered through your webhook.

2. Pipeline Stages:

- `Build Image`: The Docker image is built using the provided Dockerfile.
- `Run Container`: A Docker container is spun up based on the built image.
- `Test Container`: The running container is tested using curl.
- `Artifact`: Previous containers are removed to clean up.
- `Push Image to Docker Hub`: The built image is tagged and pushed to Docker Hub.
- `Push Image in Staging and Deploy`: This stage push the image in staging environment.
- `Push Image in Production and Deploy`: This stage push the image in production environment.
- `Post`: A notification is sent to Slack indicating if the pipeline succeed or not.
