# Kubernetes Migration Project: WordPress and MariaDB

This repository contains the complete migration process of a WordPress application from a Docker-Compose environment to a production-ready Kubernetes cluster using Helm.

---

## Project Overview

The goal of this project is to demonstrate cloud-native deployment practices, including high availability, persistent storage, automated scaling, and comprehensive monitoring.

---

## DevOps Toolchain and Prerequisites

The following tools were utilized to build, deploy, and manage the environment:

* **Minikube**: Local Kubernetes cluster environment.
* **kubectl**: Kubernetes command-line interface for cluster management.
* **Helm v3**: Package manager for defining and deploying Kubernetes applications.
* **AWS CLI**: Used for authentication and managing container images within Amazon ECR.
* **NGINX Ingress Controller**: Utilized for L7 traffic routing and host-based access.

---

## Architecture and Design Decisions

* **High Availability**: WordPress is deployed with **2 replicas** to ensure service continuity and load distribution across the cluster.
* **Persistence (StatefulSet)**: MariaDB is implemented as a **StatefulSet** with `volumeClaimTemplates`. This ensures that database identity and data persist across pod restarts, effectively separating the storage lifecycle from the pod lifecycle.
* **Service Discovery**: Internal communication is handled via Kubernetes internal DNS. WordPress connects to the database using the Service name (`mariadb`), ensuring reliable connectivity regardless of pod IP changes.
* **Security**: Sensitive data, including database credentials, is managed via **Kubernetes Secrets** to avoid hardcoding plain-text passwords in manifests.

---

## Repository Structure

* **/my_wordpress**: Original Docker-Compose configuration and environment files.
* **/raw-yamls**: Initial static Kubernetes manifests used during the first migration phase.
* **/helm-wordpress**: The final, parameterized Helm Chart designed for production deployments.
* **/monitoring**: Contains the exported Grafana dashboard JSON model for the uptime panel.

---

## Installation and Deployment

### 1. Prepare the Environment
Ensure Minikube is running and the Ingress addon is enabled:

```bash
minikube start
minikube addons enable ingress
```

### 2. Configure Host Resolution
Map the Minikube IP to the local domain by adding the following entry to your `/etc/hosts` file:

```text
[minikube-ip] wordpress.local
```

### 3. Deploy via Helm
Install the application in a dedicated namespace using the Helm Chart:

```bash
helm install my-release ./helm-wordpress -n helm-wordpress --create-namespace
```

---

## Monitoring and Observability

A comprehensive monitoring stack (Prometheus & Grafana) tracks the application's health and availability.

* **Metrics Collection**: Utilizes `kube-state-metrics` to monitor the real-time status of cluster objects.
* **Key Metric**: The `kube_pod_container_status_running` metric is used to verify the operational status of WordPress and MariaDB.
* **Visualization**: A custom State Timeline dashboard provides a visual history of uptime and service stability.
* **Dashboard as Code**: The dashboard is exported as a JSON model in the `/monitoring` directory to allow for consistent restoration and version control.
