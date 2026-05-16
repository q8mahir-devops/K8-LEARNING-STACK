# Hello World Spring Boot Application

This is a simple Spring Boot application that demonstrates how to create a RESTful web service that returns a "Hello, World!" message.

## Project Structure

```
hello-world-springboot
├── src
│   ├── main
│   │   ├── java
│   │   │   └── com
│   │   │       └── example
│   │   │           └── helloworld
│   │   │               ├── HelloWorldApplication.java
│   │   │               └── controller
│   │   │                   └── HelloController.java
│   │   └── resources
│   │       └── application.properties
│   └── test
│       └── java
│           └── com
│               └── example
│                   └── helloworld
│                       └── HelloWorldApplicationTests.java
├── pom.xml
└── README.md
```

## Prerequisites

- Java 11 or higher
- Maven

## Running the Application

1. Clone the repository:
   ```
   git clone <repository-url>
   cd hello-world-springboot
   ```

2. Build the application using Maven:
   ```
   mvn clean install
   ```

3. Run the application:
   ```
   mvn spring-boot:run
   ```

4. Access the application:
   Open your web browser and navigate to `http://localhost:8080/hello`. You should see the message "Hello, World!".

## Testing the Application

To run the tests, use the following command:
```
mvn test
```
------------------------------------------------------------------------

# 🚀 CloudNative Learning Lab — Full-Stack K8s Project

> A hands-on, end-to-end project designed to prepare you for **CKA** and **AZ-104** certifications while building a real, production-style application using modern cloud-native tools.

---

## 📌 Project Overview

- Build a fully containerised, cloud-deployed full-stack application
- Learn Kubernetes from zero through real-world configuration
- Use Azure DevOps for CI/CD pipelines end-to-end
- Deploy and monitor on **Azure Kubernetes Service (AKS)**
- Simulate multi-node clusters locally using **Red Hat VMs on VMware**
- Every service runs in a container — no exceptions

---

## 🎯 Certification Goals

| Certificate | Coverage in This Project |
|---|---|
| **CKA (Certified Kubernetes Administrator)** | Pods, Deployments, Services, ConfigMaps, Secrets, PVCs, RBAC, Ingress, Namespaces, Health Probes, Resource Limits, Rolling Updates, Cluster Setup |
| **AZ-104 (Microsoft Azure Administrator)** | AKS provisioning, Azure Container Registry (ACR), Azure Monitor, Virtual Networks, NSGs, Azure Storage, Azure Key Vault, IAM/RBAC in Azure, Azure DevOps Pipelines |

---

## 🗂️ Application Stack

### Backend
- **Spring Boot** (Java, Maven)
  - Embedded **Tomcat** is included by default in `spring-boot-starter-web` — no separate Tomcat install needed; the JAR is self-contained and runs standalone, which is ideal for containerisation
  - Exposes REST APIs
  - **Swagger / OpenAPI 3** documentation via `springdoc-openapi`
  - Connects to PostgreSQL and Redis
  - Secured with Spring Security (basic auth for MWE)

### Frontend
- **Node.js + Express** serving a Single Page Application (SPA)
- Plain HTML/CSS/JS (no heavy framework — keeps focus on infrastructure)
- Consumes backend REST APIs
- Served as a static SPA from Node.js

### Database
- **PostgreSQL** — primary relational database, running in a container
- **Flyway** — database migration tool integrated with Spring Boot
  - Runs migrations automatically on startup
  - Migration scripts versioned in `src/main/resources/db/migration/`
  - Ideal for learning schema versioning in containerised environments

### Cache / Session Store
- **Redis** — used for:
  - API response caching (reduce DB load)
  - Session management
  - Rate limiting demonstration
  - Fits naturally between the backend and PostgreSQL in the data flow

### Monitoring & Observability
- **Prometheus** — metrics scraping from all services
- **Grafana** — dashboards for visualising metrics
- **Spring Boot Actuator** — exposes `/actuator/prometheus` endpoint
- **Node Exporter** — system-level metrics from each node
- **kube-state-metrics** — Kubernetes object metrics (pod status, deployments, etc.)

### API Documentation
- **Swagger UI** via `springdoc-openapi-starter-webmvc-ui`
- Accessible at `/swagger-ui.html` on the backend service
- Describes all REST endpoints with request/response schemas

### CI/CD
- **Azure DevOps Pipelines** (YAML-based)
  - Build → Test → Dockerise → Push to ACR → Deploy to AKS
  - Separate pipelines for backend, frontend, and infrastructure
- **Azure Container Registry (ACR)** — stores Docker images

### Infrastructure
- **Azure Kubernetes Service (AKS)** — managed Kubernetes in Azure
- **Red Hat VMs on VMware** — local multi-node cluster for learning raw Kubernetes (kubeadm)
- **Helm** — Kubernetes package manager for deploying Prometheus, Grafana, ingress-nginx
- **Ingress-NGINX** — ingress controller for routing external traffic
- **cert-manager** (optional phase) — automatic TLS certificate management

---

## 🔧 Full Technology List

| Category | Tool / Technology | Purpose |
|---|---|---|
| Backend | Spring Boot + Maven | REST API server |
| Backend | Embedded Tomcat | Servlet container (built into Spring Boot JAR) |
| Backend | Spring Data JPA | ORM for PostgreSQL |
| Backend | Spring Cache + Redis | Caching layer |
| Backend | Spring Security | Basic authentication |
| Backend | Spring Boot Actuator | Health checks, metrics endpoint |
| Backend | springdoc-openapi | Swagger / OpenAPI documentation |
| Frontend | Node.js + Express | SPA host and API proxy |
| Database | PostgreSQL | Relational database |
| Migration | Flyway | Schema versioning and migration |
| Cache | Redis | Caching, session, rate limiting |
| Container | Docker | Containerisation of all services |
| Orchestration | Kubernetes (kubeadm) | Local cluster on Red Hat VMs |
| Orchestration | AKS | Managed Kubernetes on Azure |
| Package Mgmt | Helm | Deploy third-party K8s charts |
| Ingress | Ingress-NGINX | Route HTTP/S traffic into cluster |
| Monitoring | Prometheus | Metrics collection and storage |
| Monitoring | Grafana | Metrics dashboards |
| Monitoring | kube-state-metrics | K8s object state metrics |
| Monitoring | Node Exporter | Host-level metrics |
| CI/CD | Azure DevOps Pipelines | Build, test, push, deploy |
| Registry | Azure Container Registry | Docker image storage |
| Cloud | Microsoft Azure | Cloud platform (AZ-104 target) |
| Local VMs | Red Hat on VMware | Multi-node K8s cluster practice |
| Secrets | Azure Key Vault | Secrets management (AZ-104) |
| Docs | Swagger UI | Interactive API documentation |
| Networking | Kubernetes Services | ClusterIP, NodePort, LoadBalancer |
| Storage | Kubernetes PVC | Persistent volumes for PostgreSQL |
| Config | ConfigMaps & Secrets | Externalised configuration |

---

## 🧱 Architecture Overview

```
Internet
    │
    ▼
[ Ingress-NGINX ] ──────────────────────────────────────────
    │                                                       │
    ▼                                                       ▼
[ Node.js SPA ]                                  [ Spring Boot API ]
(frontend Pod)                                   (backend Pod)
                                                      │        │
                                               ┌──────┘        └──────┐
                                               ▼                      ▼
                                         [ PostgreSQL ]           [ Redis ]
                                         (statefulset)            (deployment)

Monitoring Stack (separate namespace):
[ Prometheus ] ──scrapes──▶ all pods via /actuator/prometheus
[ Grafana ]    ──queries──▶ Prometheus
[ kube-state-metrics ] ──▶ K8s object metrics
[ Node Exporter ] ──▶ host metrics

CI/CD:
Azure DevOps ──build/test──▶ Docker build ──push──▶ ACR ──deploy──▶ AKS
```

---

## 📂 Repository Structure

```
cloudnative-lab/
├── backend/                        # Spring Boot application
│   ├── src/
│   │   ├── main/java/...           # REST controllers, services, repositories
│   │   └── main/resources/
│   │       ├── application.yml     # App config (DB, Redis, Actuator)
│   │       └── db/migration/       # Flyway SQL scripts (V1__init.sql, etc.)
│   ├── Dockerfile
│   └── pom.xml
│
├── frontend/                       # Node.js SPA
│   ├── public/                     # Static HTML/CSS/JS
│   ├── server.js                   # Express server
│   ├── Dockerfile
│   └── package.json
│
├── k8s/                            # Kubernetes manifests
│   ├── namespaces/
│   ├── backend/                    # Deployment, Service, ConfigMap, Secret
│   ├── frontend/                   # Deployment, Service
│   ├── postgres/                   # StatefulSet, PVC, Service
│   ├── redis/                      # Deployment, Service
│   ├── ingress/                    # Ingress resource
│   └── monitoring/                 # Prometheus, Grafana (or Helm values)
│
├── helm/                           # Helm chart values overrides
│   ├── prometheus-values.yaml
│   └── grafana-values.yaml
│
├── azure-pipelines/                # Azure DevOps YAML pipelines
│   ├── backend-pipeline.yml
│   ├── frontend-pipeline.yml
│   └── infra-pipeline.yml
│
├── docs/                           # Architecture diagrams, notes
└── README.md
```

---

## 🪜 Learning Roadmap (Step by Step)

### Phase 1 — Local Development (Week 1–2)
- [ ] Install Docker Desktop or Docker Engine
- [ ] Write the Spring Boot hello-world API (`GET /api/hello`)
- [ ] Write the Node.js frontend that calls the backend
- [ ] Connect Spring Boot to PostgreSQL locally via Docker Compose
- [ ] Add Flyway migration scripts
- [ ] Add Redis caching to one API endpoint
- [ ] Test Swagger UI at `/swagger-ui.html`
- [ ] Confirm all services run with `docker compose up`

### Phase 2 — Kubernetes Locally on Red Hat VMs (Week 3–5)
- [ ] Provision 3 Red Hat VMs on VMware (1 control-plane + 2 worker nodes)
- [ ] Install kubeadm, kubelet, kubectl on all nodes
- [ ] Bootstrap the cluster with `kubeadm init`
- [ ] Join worker nodes with `kubeadm join`
- [ ] Install Ingress-NGINX via Helm
- [ ] Write K8s manifests: Deployment, Service, ConfigMap, Secret
- [ ] Deploy PostgreSQL as a StatefulSet with PersistentVolumeClaim
- [ ] Deploy Redis as a Deployment
- [ ] Deploy backend and frontend
- [ ] Configure Ingress to route `/api/*` to backend and `/` to frontend
- [ ] Add liveness and readiness probes
- [ ] Set resource requests and limits on all pods
- [ ] Practice rolling updates and rollbacks
- [ ] Practice `kubectl` commands extensively (CKA preparation)

### Phase 3 — Monitoring (Week 6)
- [ ] Install Prometheus via Helm into `monitoring` namespace
- [ ] Install Grafana via Helm
- [ ] Configure Spring Boot Actuator metrics endpoint
- [ ] Import Kubernetes dashboard in Grafana
- [ ] Create a custom dashboard for API response time
- [ ] Set up basic alerting rules in Prometheus

### Phase 4 — Azure & CI/CD (Week 7–9)
- [ ] Create Azure account and Resource Group
- [ ] Provision AKS cluster (1 control-plane managed + 2 worker nodes)
- [ ] Create Azure Container Registry (ACR)
- [ ] Link ACR to AKS
- [ ] Set up Azure DevOps organisation and project
- [ ] Write `azure-pipelines/backend-pipeline.yml`:
  - Build Spring Boot JAR with Maven
  - Run unit tests
  - Build Docker image
  - Push to ACR
  - Deploy to AKS
- [ ] Write `azure-pipelines/frontend-pipeline.yml` (same pattern)
- [ ] Set up Azure Key Vault and inject secrets into AKS via CSI driver
- [ ] Configure Azure Monitor and Container Insights for AKS

### Phase 5 — Advanced K8s Concepts (Week 10–12)
- [ ] Configure Horizontal Pod Autoscaler (HPA) for the backend
- [ ] Implement RBAC — create roles, role bindings, service accounts
- [ ] Practice Network Policies to restrict pod-to-pod communication
- [ ] Configure multiple namespaces (dev / staging / prod)
- [ ] Taint and tolerate nodes for workload isolation
- [ ] Practice etcd backup and restore (CKA exam topic)
- [ ] Upgrade a Kubernetes cluster version with kubeadm
- [ ] Practice troubleshooting broken pods, services, ingress

---

## ❓ OpenShift — Do You Need It?

**Short answer: No, not for CKA or AZ-104.** OpenShift is Red Hat's enterprise Kubernetes distribution. It adds its own abstractions (Routes instead of Ingress, ImageStreams, Projects instead of Namespaces). Since you already have Red Hat VMs, you *could* explore it, but:

- CKA is based on **vanilla Kubernetes** — OpenShift is not tested
- AZ-104 focuses on **Azure services** — no OpenShift involvement
- Learning OpenShift on top of vanilla K8s at this stage would add confusion

**Recommendation:** Complete this project with vanilla Kubernetes. If you later pursue the **Red Hat OpenShift Administrator (EX280)** certificate, the K8s foundation from this project will transfer directly.

---

## 🍃 Why Embedded Tomcat in Spring Boot?

Spring Boot's `spring-boot-starter-web` dependency includes an **embedded Tomcat** server inside the final JAR file. This means:

- No separate Tomcat installation is required
- The JAR is fully self-contained: `java -jar app.jar` starts the server
- This is the standard and recommended approach for containerised Spring Boot apps
- The Docker image simply runs the JAR — clean, minimal, portable
- You can swap to Jetty or Undertow if needed, but Tomcat is the default

---

## 🛡️ Why Flyway for Database Migrations?

- Flyway tracks which SQL scripts have been applied using a metadata table (`flyway_schema_history`)
- Scripts are named `V1__create_messages.sql`, `V2__add_index.sql`, etc.
- On Spring Boot startup, Flyway automatically runs any pending migrations
- This prevents "it works on my machine" DB schema mismatches between environments
- **Liquibase** is an alternative but Flyway is simpler for beginners

---

## 🔴 Why Redis?

Redis sits between the backend and PostgreSQL as a high-speed in-memory store:

- **API caching** — cache the response of `GET /api/messages` for 60 seconds to reduce DB queries
- **Session storage** — store user sessions outside the JVM (stateless pods)
- **Rate limiting** — count API calls per IP using Redis atomic counters
- In Kubernetes, Redis runs as a separate Deployment with a ClusterIP Service

---

## 📋 Prerequisites

- Java 17+
- Maven 3.8+
- Node.js 18+
- Docker Engine
- kubectl
- Helm 3
- Azure CLI
- VMware Workstation / ESXi with 3 Red Hat VMs (2 vCPU, 4 GB RAM each recommended)
- Azure subscription (free tier works for AKS with small node sizes)
- Azure DevOps account (free tier)

---

## 🤝 Contributing

This is a personal learning lab. Fork it, break things, fix them, and learn.

---

## 📄 License

MIT — use freely for learning and certification preparation.