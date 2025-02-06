---
sidebar_position: 5
---

# Deploy Kubernetes

## Overview
Kubernetes is an open-source container orchestration system for automating the deployment, scaling, and management of containerized applications. It was originally designed by Google and is now maintained by the Cloud Native Computing Foundation (CNCF). Kubernetes provides a platform for self-healing, horizontal pod autoscaling, and rolling updates, making it easier to deploy and manage containerized applications. We use Kubernetes to automate the deployment of our containerized applications, ensuring consistent and efficient deployment across our infrastructure.

### Explanation of `deploy_dev` Job

```yaml
deploy_dev:
  name: Deploy Kubernetes
  runs-on: ubuntu-24.04
  needs: build_dev
  timeout-minutes: 10
  env:
    CPU_LIMIT: ${{ vars.DEV_CPU_LIMIT }}
    CPU_REQUEST: ${{ vars.DEV_CPU_REQUEST }}
    KUBE_SECRET: ${{ secrets.DEV_KUBE_SECRET }}
    KUBE_URL: ${{ secrets.DEV_KUBE_URL }}
    MEMORY_LIMIT: ${{ vars.DEV_MEMORY_LIMIT }}
    MEMORY_REQUEST: ${{ vars.DEV_MEMORY_REQUEST }}
    NAMESPACE: ${{ vars.DEV_NAMESPACE }}
```

- **`deploy_dev`**: This section defines a job within the GitHub Actions workflow.
- **`name: Deploy Kubernetes`**: Specifies the name of the job as "Deploy Kubernetes".
- **`runs-on: ubuntu-24.04`**: Indicates that the job will run on a GitHub Actions runner with Ubuntu 24.04.
- **`needs: build_dev`**: Specifies that this job is dependent on the successful completion of the `build_dev` job.
- **`timeout-minutes: 10`**: Sets a timeout for the job, terminating it if it exceeds 10 minutes.
- **`env`**: Defines the environment variables for the job.

KUBE_SECRET : obtained from the yaml file in the secrets, usually named with the name of the deployer <br />
KUBE_URL : obtained from the server address or IP address

### Checkout and Lowercase Image Name

```yaml
- name: Checkout
  uses: actions/checkout@v4

- name: Lowercase image name
  run: |-
    echo "IMAGE_NAME=$(echo ${{ env.IMAGE_NAME }} | tr '[:upper:]' '[:lower:]')" >> $GITHUB_ENV
```

- **Checkout Step**: 
    - **`uses: actions/checkout@v4`**: This action checks out the repository's code onto the runner. This is typically the first step in any GitHub Actions workflow, ensuring that you can work with the repository's contents.
  
- **Lowercase Image Name**:
    - **`run: ...`**: This command converts the `IMAGE_NAME` environment variable to lowercase and stores it in the GitHub environment variable `IMAGE_NAME`. This ensures that the image name is in lowercase for consistency and compatibility with Kubernetes and Docker naming conventions.

### Deployment Preparation

```yaml
- name: prepare k8s deployment
  run: |
    sed -i "s|{{ APP_NAME }}|$APP_NAME|g" ./deployment/k8s/d.yml
    sed -i "s|{{ CPU_LIMIT }}|$CPU_LIMIT|g" ./deployment/k8s/d.yml
    sed -i "s|{{ CPU_REQUEST }}|$CPU_REQUEST|g" ./deployment/k8s/d.yml
    sed -i "s|{{ IMAGE_NAME }}|$IMAGE_NAME|g" ./deployment/k8s/d.yml
    sed -i "s|{{ MEMORY_LIMIT }}|$MEMORY_LIMIT|g" ./deployment/k8s/d.yml
    sed -i "s|{{ MEMORY_REQUEST }}|$MEMORY_REQUEST|g" ./deployment/k8s/d.yml
    sed -i "s|{{ NAMESPACE }}|$NAMESPACE|g" ./deployment/k8s/d.yml
    sed -i "s|{{ PORT }}|$PORT|g" ./deployment/k8s/d.yml
    sed -i "s|{{ PROTOCOL }}|$PROTOCOL|g" ./deployment/k8s/d.yml
```

This step prepares the Kubernetes deployment YAML file (`deployment/k8s/d.yml`) by replacing placeholders (like `{{ APP_NAME }}`) with actual environment variables. 
- The `sed` commands find and replace placeholders in the YAML file with dynamic values from the environment variables, such as `APP_NAME`, `NAMESPACE`, `IMAGE_NAME`, and resource limits like `CPU_LIMIT` and `MEMORY_LIMIT`. This makes the deployment file ready for actual deployment.

---

### Kubernetes Deployment Manifest (`deployment/k8s/d.yml`)

This is a Kubernetes deployment YAML file with placeholders that will be replaced by values during the GitHub Actions workflow. 

- **`apiVersion: apps/v1`**: Specifies the API version for Kubernetes resources.
- **`kind: Deployment`**: This indicates that we are defining a Kubernetes Deployment resource.
- **`metadata`**: Contains the name and namespace for the deployment.
  - **`name: {{ APP_NAME }}`**: Placeholder for the application name, which will be replaced with the actual name during the workflow.
  - **`namespace: {{ NAMESPACE }}`**: Placeholder for the Kubernetes namespace to deploy into.
- **`spec`**: Contains the configuration for the deployment.
  - **`replicas: 1`**: Specifies the number of pod replicas to run.
  - **`containers`**: Defines the container for the application, including the image (`{{ IMAGE_NAME }}`) and the port it exposes.
  - **`resources`**: Specifies resource limits (CPU and memory) and requests for the container.
  - **`env`**: Environment variables are injected into the container, with secrets being pulled from a Kubernetes secret (`{{ APP_NAME }}-secrets`).

---

### Explanation of Kubernetes Context and Deployment to Cluster

```yaml
- name: Set Context
  uses: azure/k8s-set-context@v1
  with:
    method: service-account
    k8s-url: ${{ env.KUBE_URL }}
    k8s-secret: ${{ env.KUBE_SECRET }}

- name: Deploy to the Kubernetes Cluster
  uses: azure/k8s-deploy@v1
  with:
    namespace: ${{ env.NAMESPACE }}
    manifests: ./deployment/k8s/d.yml
```

- **Set Context**: 
  - This step sets up the context for the Kubernetes deployment using the `azure/k8s-set-context` action. It configures the connection to the Kubernetes cluster by providing:
    - **`k8s-url`**: The URL of the Kubernetes API (stored in GitHub Secrets).
    - **`k8s-secret`**: The Kubernetes secret used to authenticate the connection (also stored in GitHub Secrets).
    - **`method: service-account`**: This method authenticates using a service account, typically used in automated CI/CD workflows.
  
- **Deploy to the Kubernetes Cluster**:
  - This step deploys the Kubernetes resources defined in the `d.yml` manifest to the cluster using the `azure/k8s-deploy` action.
  - **`namespace`**: Specifies the Kubernetes namespace where the resources will be deployed.
  - **`manifests`**: Points to the location of the deployment YAML file (`./deployment/k8s/d.yml`), which contains the Kubernetes deployment configuration. 

This completes the deployment of the application to the Kubernetes cluster using the configured context and manifests.

deployment file
```yaml
  deploy_dev:
    name: Deploy Kubernetes
    runs-on: ubuntu-24.04
    needs: build_dev
    timeout-minutes: 10
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Lowercase image name
        run: |-
          echo "IMAGE_NAME=$(echo ${{ env.IMAGE_NAME }} | tr '[:upper:]' '[:lower:]')" >> $GITHUB_ENV

      - name: perpare k8s deployment
        run: |
          sed -i "s|{{ APP_NAME }}|$APP_NAME|g" ./deployment/k8s/d.yml
          sed -i "s|{{ CPU_LIMIT }}|$CPU_LIMIT|g" ./deployment/k8s/d.yml
          sed -i "s|{{ CPU_REQUEST }}|$CPU_REQUEST|g" ./deployment/k8s/d.yml
          sed -i "s|{{ IMAGE_NAME }}|$IMAGE_NAME|g" ./deployment/k8s/d.yml
          sed -i "s|{{ MEMORY_LIMIT }}|$MEMORY_LIMIT|g" ./deployment/k8s/d.yml
          sed -i "s|{{ MEMORY_REQUEST }}|$MEMORY_REQUEST|g" ./deployment/k8s/d.yml
          sed -i "s|{{ NAMESPACE }}|$NAMESPACE|g" ./deployment/k8s/d.yml
          sed -i "s|{{ PORT }}|$PORT|g" ./deployment/k8s/d.yml
          sed -i "s|{{ PROTOCOL }}|$PROTOCOL|g" ./deployment/k8s/d.yml

      - name: Set Context
        uses: azure/k8s-set-context@v1
        with:
          method: service-account
          k8s-url: ${{ env.KUBE_URL }}
          k8s-secret: ${{ env.KUBE_SECRET }}
      
      - name: Deploy to the Kubernetes Cluster
        uses: azure/k8s-deploy@v1
        with:
          namespace: ${{ env.NAMESPACE }}
          manifests: ./deployment/k8s/d.yml
```

