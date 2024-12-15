# Helm Chart for Spring Boot Application with PostgreSQL

This Helm chart deploys a Spring Boot application that connects to a PostgreSQL database. The chart manages the application and database resources efficiently using Kubernetes objects like Deployments, Services, Secrets, and PersistentVolumes.

---

## Features

1. **Spring Boot Application**
    - Runs on port `8080`.
    - Deployed as a `Deployment` with `2` replicas.
    - Image pulled from Docker Hub: `naveenalla/spring-app-helm`.

2. **PostgreSQL Database**
    - Persistent storage enabled using a PersistentVolumeClaim (`postgres-pvc`).
    - Credentials and configuration are managed using Kubernetes Secrets.
    - Default credentials:
        - Username: `postgres`
        - Password: `postgres`
        - Database: `springdb`
    - Storage size: `1Gi`.

3. **Service Configuration**
    - PostgreSQL is exposed as a `ClusterIP` service on port `5432`.
    - Application is exposed as a `NodePort` service on port `8080`.

---

## Files and Configuration

### Chart Files:
- `postgres-pv.yaml`: Configures persistent storage for PostgreSQL.
- `postgres-pvc.yaml`: Claims storage for PostgreSQL.
- `postgres-deployment.yaml`: Deploys PostgreSQL as a Kubernetes deployment.
- `postgres-service.yaml`: Exposes PostgreSQL as a Kubernetes service.
- `secrets.yaml`: Stores PostgreSQL credentials securely.
- `spring-app-deployment.yaml`: Deploys the Spring Boot application.
- `spring-app-service.yaml`: Exposes the Spring Boot application as a service.

### Values.yaml Configuration:
```yaml
postgres:
  username: postgres
  password: postgres
  database: springdb
  persistence:
    enabled: true
    storageSize: 1Gi

springApp:
  image: naveenalla/spring-app-helm
  replicas: 2

service:
  type: NodePort
  port: 8080
```

---

## Prerequisites

1. Kubernetes cluster (1.20+) with `kubectl` configured.
2. Helm (3.x) installed on the cluster.
3. Persistent storage provisioner for the cluster.
4. minikube (optional) for local testing.

---

## Installation & Deployment steps

1.  Add repository helm-chart files from the artifacthub.io.
    ```bash
    helm repo add naveenalla-springboot https://naveenalla3000.github.io/springboot-helm/
    ```
2.  Update the repository.
    ```bash
    helm repo update
    ```
3.  Install the Helm chart with a release name `spring-app`.
    ```bash
    helm install my-spring-helm naveenalla-springboot/spring-helm
    ```
4.  Verify the deployment.
    ```bash
    kubectl get all
    ```


---

## Access the Application

1. Verify the NodePort service created for the application.
   ```bash
   kubectl get svc
   ```
2. Access the application using the NodePort service.
   ```bash
   kubectl port-forward service/my-spring-helm-spring-app-service 8080:8080
   ```
3. Open the application in a browser using the URL: `http://localhost:8080`.

---

## Flow Diagram

Below is the deployment architecture for the Helm chart:

```plaintext
 +------------------+      +-----------------------+      +-------------------+
 |                  |      |                       |      |                   |
 |  postgresql      |      |       cluster-ip      |      |     springboot    |
 |     pod          |----->|       db-service      |----->|     application   |  
 |                  |      |                       |      |                   |
 +--------+---------+      +-----------+-----------+      +---------+---------+
          |                                                         |
          |                                                         |                    
 +--------v---------+                                     +---------v---------+
 |                  |                                     |                   | 
 |     persistent   |                                     |    NodePort       |
 |    volume claim  |                                     |    app-service    |
 |                  |                                     |                   |                        
 +------------------+                                     +-------------------+
           |                                                       |
           |                                                       |
 +---------v---------+                                   +---------v---------+
 |                   |                                   |                   |            
 |    persistent     |                                   |    web browser    |
 |     storage       |                                   |                   |
 |                   |                                   +-------------------+
 +-------------------+
             
```

---

## Notes

- Ensure the Kubernetes cluster has sufficient resources to run both the application and database.
- Backup important data stored in PostgreSQL regularly.

---

## Uninstallation

To delete the Helm release:
```bash
helm uninstall my-spring-helm
```

This will remove all resources associated with the release.

