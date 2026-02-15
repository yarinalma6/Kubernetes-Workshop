# Kubernetes-Workshop
# Kubernetes Migration Project: WordPress and MariaDB

This repository demonstrates the end-to-end migration of a WordPress application from a local Docker-Compose environment to a production-ready Kubernetes cluster using Helm.

## DevOps Toolchain and Prerequisites
The following tools were utilized to build and deploy this environment:
* **Minikube**: Local Kubernetes cluster environment.
* **kubectl**: Kubernetes command-line interface.
* **Helm v3**: Package manager for Kubernetes applications.
* **AWS CLI**: Used for authentication and managing container images within Amazon ECR.
* **NGINX Ingress Controller**: Utilized for advanced traffic routing and host-based access.

## Architecture and Design Decisions
* **High Availability**: The WordPress application is deployed with a minimum of 2 replicas to ensure service continuity and load distribution.
* **Persistence (StatefulSet)**: MariaDB is implemented as a StatefulSet with volumeClaimTemplates. This ensures that database identity and data persist across pod restarts or rescheduling, separating the storage lifecycle from the pod lifecycle.
* **Service Discovery**: Internal communication between WordPress and the database is handled via Kubernetes internal DNS, using Service names to ensure reliable connectivity.
* **Security**: Sensitive information, such as database credentials, is managed through Kubernetes Secrets rather than being hardcoded in the manifests.

## Repository Structure
* **/my_wordpress**: Contains the original Docker-Compose configuration and environment files.
* **/raw-yamls**: Contains the initial, static Kubernetes manifests used during the first phase of migration.
* **/helm-wordpress**: The final, parameterized Helm Chart designed for production-grade deployments.
* **/monitoring**: Contains the exported Grafana dashboard JSON model.

## Installation and Deployment
1. **Prepare the Environment**:
   Ensure Minikube is running and the Ingress addon is enabled:
   ```bash
   minikube start
   minikube addons enable ingress
Configure Host Resolution: Map the Minikube IP to the local domain by adding the following entry to your /etc/hosts file:

Plaintext
[minikube-ip] wordpress.local
Deploy via Helm: Run the following command to install the application in a dedicated namespace:

Bash
helm install my-release ./helm-wordpress -n helm-wordpress --create-namespace
Monitoring and Observability
A comprehensive monitoring stack consisting of Prometheus and Grafana was deployed to track application health.

Metrics Collection: Utilizes kube-state-metrics to monitor the status of cluster objects.

Key Metric: The kube_pod_container_status_running metric is used to track the real-time availability of the WordPress and MariaDB containers.

Visualization: A custom State Timeline dashboard was created in Grafana to visualize uptime and service stability over time.

Persistence: The dashboard is stored as a JSON model in the /monitoring directory, following the "Dashboard as Code" principle for easy restoration.
