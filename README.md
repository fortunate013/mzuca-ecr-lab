# mzuca-ecr-lab

## Project overview

This project demonstrates how to containerize a Node.js application with Docker and publish the resulting image to a private Amazon Elastic Container Registry (Amazon ECR) repository.

## Application

The Docker container starts the application with `node server.js`. The Dockerfile declares port `3000`; the application must listen on that port for the container to serve requests on it.

## Dockerfile
FROM node:22-alpine
WORKDIR /app
COPY server.js .
EXPOSE 3000
CMD ["node", "server.js"]

## Build the image
Run this from the project directory, where the Dockerfile is located:
docker build -t mzuca-ecr-lab:1.0 .

## AWS Region

The ECR repository is hosted in af-south-1

## ECR repository name

mzuca-ecr-lab

## Authentication process

Select the configured AWS CLI profile and set the region and registry address:

export AWS_PROFILE=Fortunate
export REGION=af-south-1
export REGISTRY="$(aws sts get-caller-identity --query Account --output text).dkr.ecr.${REGION}.amazonaws.com"
Authenticate Docker to Amazon ECR:
aws ecr get-login-password --region "$REGION" \
  | docker login --username AWS --password-stdin "$REGISTRY"

## Image publishing command

docker tag mzuca-ecr-lab:1.0 "$REGISTRY/mzuca-ecr-lab:1.0"

## Image publishing command

docker push "$REGISTRY/mzuca-ecr-lab:1.0"

## Image verification evidence

Verify that the `1.0` tag exists in the ECR repository:
aws ecr describe-images \
  --repository-name mzuca-ecr-lab \
  --image-ids imageTag=1.0 \
  --region af-south-1 \
  --query 'imageDetails[0].[imageTags[0],imageDigest,imagePushedAt]' \
  --output table.
  A screenshot of the created images will be pasted on the screenshots folder.
