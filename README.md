# Node.js CI/CD Pipeline

This repository demonstrates a CI/CD pipeline for a Node.js application. The pipeline automates the testing, building, and deployment of the application to a Kubernetes cluster using GitHub Actions, Docker, and Kubernetes.

The pipeline consists of the following stages:
1. **CI (Continuous Integration)**: Runs tests automatically on pull requests to ensure that the code is functional before merging.
2. **CD (Continuous Deployment)**: Builds a Docker image and deploys the application to a Kubernetes cluster upon pushing changes to the `main` branch.
3. **Notifications**: Sends notifications regarding the success or failure of the deployment process. Here I simply print a message as failure or succes. We can use notification application such as slack.


## Approach

### 1. **CI Pipeline (`ci.yml`)**

The CI pipeline is designed to run automatically on pull requests to the `main` branch. The key steps in this pipeline are:

1. **Checkout Code**: The pipeline checks out the code from the GitHub repository using `actions/checkout`.
2. **Setup Node.js**: It sets up the Node.js environment using the `actions/setup-node` action, specifying the required Node.js version.
3. **Install Dependencies**: It installs the necessary dependencies specified in the `package.json` file using `npm install`.
4. **Run Tests**: The pipeline runs the tests (currently a placeholder in `npm test`) to verify that the application is working as expected.

This process helps ensure that any new changes to the codebase are tested before merging them into the main branch.

### 2. **CD Pipeline (`cd.yml`)**

The CD pipeline runs whenever changes are pushed to the `main` branch. It automates the process of building, testing, and deploying the application to a Kubernetes cluster. The steps involved are:

1. **Checkout Code**: Similar to the CI pipeline, the code is checked out using `actions/checkout`.
2. **Build Docker Image**: A Docker image for the Node.js app is built using the `Dockerfile`. The image is tagged with `latest` and pushed to Docker Hub. The credentials for Docker Hub (`DOCKER_USERNAME` and `DOCKER_PASSWORD`) are securely stored as GitHub secrets.
3. **Configure kubectl**: The Kubernetes CLI (`kubectl`) is set up using the `azure/setup-kubectl` action. This step allows the pipeline to interact with the Kubernetes cluster.
4. **Deploy to Kubernetes**: The pipeline applies Kubernetes manifests (`namespace.yaml`, `deployment.yaml`, and `service.yaml`) to deploy the application to the Kubernetes cluster. It ensures that the Kubernetes resources are properly configured and deployed.
5. **Notifications**: After the deployment, the pipeline sends notifications about the success or failure of the deployment. If the deployment succeeds, a success message is displayed. In case of failure, a failure message is shown.

### 3. **Kubernetes Configuration**

The `k8s` directory contains Kubernetes manifests that define the resources needed for deployment:

- **`namespace.yaml`**: Creates a separate namespace in the Kubernetes cluster to isolate the application.
- **`deployment.yaml`**: Defines the Deployment resource, specifying how the Node.js application should run in the Kubernetes cluster, including the number of replicas and the Docker image to use.
- **`service.yaml`**: Defines the Service resource to expose the application, enabling access to the Node.js app via a LoadBalancer or NodePort.

### 4. **Dockerfile**

The `Dockerfile` is responsible for containerizing the Node.js application. The steps in the Dockerfile include:

1. Using the official Node.js base image (`node:18-alpine`).
2. Installing application dependencies via `npm install`.
3. Copying the application code into the container.
4. Exposing port `3000`, the default port for the Node.js app.

### 5. **GitHub Actions Secrets**

For security and proper execution of the pipelines, we use GitHub Actions secrets. These secrets include:

- **`DOCKER_USERNAME`** and **`DOCKER_PASSWORD`**: Docker Hub credentials to push the built image.
- **`KUBECONFIG`**: The kubeconfig file used for accessing the Kubernetes cluster. This should be stored as a base64-encoded string in GitHub secrets.

### 6. **Notifications**

The CI/CD pipeline includes a basic notification system:

- **Success Notification**: After the successful deployment, the pipeline outputs a success message.
- **Failure Notification**: If the deployment fails, an error message is displayed.






