---
sidebar_position: 1
---

# Build Image Registry

## Overview

### What is an Image Registry?

An image registry is a centralized repository used for storing, managing, and distributing container images. It enables teams to store their containerized applications and share them across environments, ensuring consistency and easy deployment.

### What Do We Use?

- **Docker Hub** – Docker's official registry for storing and sharing container images.
- **GitHub Container Registry (ghcr.io)** – GitHub’s registry for storing Docker images directly within GitHub repositories.

---

## Github Actions Workflow for Image Building

To automate the process of building and deploying container images, we use GitHub Actions. The workflow should be organized as follows:

### Folder Structure

Create a folder structure within your repository:
```bash
.github/workflows
```

Inside this folder, create a file named `deployment.yml`. Based on the environment, you may have the following files:
- `deploy-dev.yml`
- `deploy-prod.yml`

These files are used to build and push container images to the registry, each corresponding to a specific environment (development, production).

### Deployment Trigger

- **Development Environment**: The workflow will automatically run whenever there is a push or merge to the `dev` branch.
- **Staging and Production Environments**: Deployment to these environments is triggered manually via `workflow_dispatch` or external triggers.

Here’s how the GitHub Actions workflow for the development environment can be triggered:

```yaml
name: GitHub Actions - Development

on:
  push:
    branches:
    - dev
  workflow_dispatch:
```

### Define Image Registry Name

The format for the image registry name is as follows:

```yaml
env:
  IMAGE_NAME: ${{ vars.CR }}/${{ github.repository }}:${{ github.head_ref || github.ref_name }}-${{ github.sha }}
```

Where:
- `vars.CR` is the container registry (e.g., `ghcr.io`).
- `github.repository` represents the repository name.
- `${{ github.head_ref || github.ref_name }}` denotes the branch name.
- `${{ github.sha }}` is the commit hash.


## Jobs Configuration

### 1. Build Backend Service Image

The `build_dev` job will build the Docker image and push it to the registry. This job runs only when there is a push or merge to the `dev` branch.

```yaml
jobs:
  build_dev:
    name: Build Development
    runs-on: ubuntu-24.04
    if: contains(github.ref, 'dev')
    timeout-minutes: 10
    permissions:
      contents: read
      packages: write
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Lowercase Repository Name
        run: |
          echo "IMAGE_NAME=$(echo ${{ env.IMAGE_NAME }} | tr '[:upper:]' '[:lower:]')" >> $GITHUB_ENV

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2

      - name: Login to GitHub Container Registry
        uses: docker/login-action@v3
        with:
          registry: ${{ vars.CR }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Build and push Docker image
        uses: docker/build-push-action@v6
        with:
          context: .
          push: true
          tags: ${{ env.IMAGE_NAME }}
          cache-from: type=gha
          cache-to: type=gha,mode=max
          file: Dockerfile
```

### 2. Build Front-End (FE) Image

If you have a front-end service that requires building and pushing to the registry, use the following job configuration:

```yaml
      - name: Build and push Docker image
        uses: docker/build-push-action@v6
        with:
          context: .
          push: true
          tags: ${{ env.IMAGE_NAME }}
          cache-from: type=gha
          cache-to: type=gha,mode=max
          file: Dockerfile
          build-args: |
            VITE_API_BASE_URL=${{ vars.DEV_VITE_API_BASE_URL }}
```

### Permissions for GitHub Runner

Before the build process begins, ensure the GitHub runner has the necessary permissions to interact with the Docker registry. This is done by configuring the `docker/login-action` step with GitHub’s token and the registry.

```yaml
- name: Login to GitHub Container Registry
  uses: docker/login-action@v3
  with:
    registry: ${{ vars.CR }}
    username: ${{ github.actor }}
    password: ${{ secrets.GITHUB_TOKEN }}
```


## Full Example Workflow for Development Image

Here is the complete GitHub Actions workflow for building and pushing a Docker image for the development environment:

```yaml
name: GitHub Actions Development

on:
  push:
    branches:
    - dev
  workflow_dispatch:

env:
  IMAGE_NAME: ${{ vars.CR }}/${{ github.repository }}:${{ github.head_ref || github.ref_name }}-${{ github.sha }}

jobs:
  build_dev:
    name: Build Development
    runs-on: ubuntu-24.04
    if: contains(github.ref, 'dev')
    timeout-minutes: 10
    permissions:
      contents: read
      packages: write
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Lowercase Repository Name
        run: |
          echo "IMAGE_NAME=$(echo ${{ env.IMAGE_NAME }} | tr '[:upper:]' '[:lower:]')" >> $GITHUB_ENV

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2

      - name: Login to GitHub Container Registry
        uses: docker/login-action@v3
        with:
          registry: ${{ vars.CR }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Build and push Docker image
        uses: docker/build-push-action@v6
        with:
          context: .
          push: true
          tags: ${{ env.IMAGE_NAME }}
          cache-from: type=gha
          cache-to: type=gha,mode=max
          file: Dockerfile
```

## Conclusion

This workflow allows you to automate the process of building, tagging, and pushing Docker images to your container registry. It helps to ensure that the correct images are always deployed to the right environment, minimizing the risk of errors and ensuring consistency across development, staging, and production environments.