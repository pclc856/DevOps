# Kubernetes Complete Guide for DevOps Interview

## 1. Kubernetes Fundamentals

### What is Kubernetes (K8s)?
- **Container orchestration platform** for automating deployment, scaling, and management
- Originally developed by **Google**, now maintained by CNCF
- Manages **clusters** of containers across multiple hosts
- Provides **self-healing**, **auto-scaling**, and **load balancing**

### Why Kubernetes?
- **High Availability** - No downtime
- **Scalability** - Scale up/down based on demand
- **Disaster Recovery** - Backup and restore
- **Service Discovery** - Automatic networking
- **Rolling Updates** - Zero-downtime deployments
- **Self-Healing** - Automatically restarts failed containers

### Kubernetes Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    CONTROL PLANE                         │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  │
│  │  API Server  │  │  Scheduler   │  │  Controller  │  │
│  │              │  │              │  │   Manager    │  │
│  └──────────────┘  └──────────────┘  └──────────────┘  │
│  ┌──────────────────────────────────────────────────┐  │
│  │              etcd (Cluster Store)                 │  │
│  └──────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────┘
                           │
        ┌──────────────────┼──────────────────┐
        │                  │                  │
┌───────▼────────┐  ┌──────▼────────┐  ┌─────▼─────────┐
│  WORKER NODE 1 │  │ WORKER NODE 2 │  │ WORKER NODE 3 │
│  ┌──────────┐  │  │  ┌──────────┐ │  │  ┌──────────┐ │
│  │  Kubelet │  │  │  │  Kubelet │ │  │  │  Kubelet │ │
│  └──────────┘  │  │  └──────────┘ │  │  └──────────┘ │
│  ┌──────────┐  │  │  ┌──────────┐ │  │  ┌──────────┐ │
│  │Kube-Proxy│  │  │  │Kube-Proxy│ │  │  │Kube-Proxy│ │
│  └──────────┘  │  │  └──────────┘ │  │  └──────────┘ │
│  ┌──────────┐  │  │  ┌──────────┐ │  │  ┌──────────┐ │
│  │Container │  │  │  │Container │ │  │  │Container │ │
│  │ Runtime  │  │  │  │ Runtime  │ │  │  │ Runtime  │ │
│  └──────────┘  │  │  └──────────┘ │  │  └──────────┘ │
│   Pods, Pods   │  │   Pods, Pods  │  │   Pods, Pods  │
└────────────────┘  └───────────────┘  └───────────────┘
```

### Control Plane Components

| Component | Purpose |
|-----------|---------|
| **API Server** | Frontend for K8s control plane, exposes REST API |
| **etcd** | Distributed key-value store for cluster data |
| **Scheduler** | Assigns pods to nodes based on resource requirements |
| **Controller Manager** | Runs controllers (Node, Replication, Endpoints, etc.) |
| **Cloud Controller Manager** | Interfaces with cloud provider APIs |

### Worker Node Components

| Component | Purpose |
|-----------|---------|
| **Kubelet** | Agent that ensures containers are running in pods |
| **Kube-proxy** | Network proxy maintaining network rules |
| **Container Runtime** | Software for running containers (Docker, containerd, CRI-O) |

---

## 2. Core Kubernetes Objects

### Pod
- **Smallest deployable unit** in Kubernetes
- Contains one or more containers
- Shares network namespace and storage
- Has unique IP address

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
    tier: frontend
spec:
  containers:
  - name: nginx
    image: nginx:1.21
    ports:
    - containerPort: 80
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
    env:
    - name: ENV_VAR
      value: "production"
    volumeMounts:
    - name: data
      mountPath: /data
  volumes:
  - name: data
    emptyDir: {}
```

### ReplicaSet
- Maintains a stable set of replica pods
- Ensures specified number of pods are running
- Usually managed by Deployment

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-replicaset
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.21
        ports:
        - containerPort: 80
```

### Deployment
- Manages ReplicaSets and Pods
- Provides declarative updates
- Supports rolling updates and rollbacks
- **Most commonly used object**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.21
        ports:
        - containerPort: 80
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
```

### Service
- Exposes pods to network traffic
- Provides stable endpoint for pods
- Load balances traffic across pods

**Types of Services:**
- **ClusterIP** (default) - Internal cluster access only
- **NodePort** - Exposes on each node's IP at a static port
- **LoadBalancer** - Creates external load balancer (cloud)
- **ExternalName** - Maps to external DNS name

```yaml
# ClusterIP Service
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: ClusterIP
  selector:
    app: nginx
  ports:
  - protocol: TCP
    port: 80          # Service port
    targetPort: 80    # Container port

---
# NodePort Service
apiVersion: v1
kind: Service
metadata:
  name: nginx-nodeport
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
    nodePort: 30080   # 30000-32767 range

---
# LoadBalancer Service
apiVersion: v1
kind: Service
metadata:
  name: nginx-lb
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
```

### Namespace
- Virtual clusters within physical cluster
- Provides resource isolation
- Default namespaces: default, kube-system, kube-public

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: development
---
apiVersion: v1
kind: Namespace
metadata:
  name: production
```

### ConfigMap
- Stores non-sensitive configuration data
- Decouples configuration from container images

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  database_url: "postgres://db:5432"
  log_level: "info"
  app.properties: |
    color.good=purple
    color.bad=yellow
    allow.textmode=true
```

**Using ConfigMap in Pod:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: app
    image: myapp:latest
    env:
    - name: DATABASE_URL
      valueFrom:
        configMapKeyRef:
          name: app-config
          key: database_url
    volumeMounts:
    - name: config
      mountPath: /etc/config
  volumes:
  - name: config
    configMap:
      name: app-config
```

### Secret
- Stores sensitive data (passwords, tokens, keys)
- Base64 encoded (not encrypted!)

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  username: YWRtaW4=      # base64 encoded "admin"
  password: cGFzc3dvcmQ=  # base64 encoded "password"
---
# Alternative: stringData (auto-encoded)
apiVersion: v1
kind: Secret
metadata:
  name: db-secret-2
type: Opaque
stringData:
  username: admin
  password: password
```

**Using Secret in Pod:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: app
    image: myapp:latest
    env:
    - name: DB_USERNAME
      valueFrom:
        secretKeyRef:
          name: db-secret
          key: username
    - name: DB_PASSWORD
      valueFrom:
        secretKeyRef:
          name: db-secret
          key: password
```

### PersistentVolume (PV) & PersistentVolumeClaim (PVC)
- **PV**: Cluster-level storage resource
- **PVC**: Request for storage by user

```yaml
# PersistentVolume
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-storage
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: standard
  hostPath:
    path: /mnt/data

---
# PersistentVolumeClaim
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-storage
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
  storageClassName: standard

---
# Using PVC in Pod
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: app
    image: nginx
    volumeMounts:
    - mountPath: /data
      name: storage
  volumes:
  - name: storage
    persistentVolumeClaim:
      claimName: pvc-storage
```

**Access Modes:**
- **ReadWriteOnce (RWO)** - Single node read-write
- **ReadOnlyMany (ROX)** - Multiple nodes read-only
- **ReadWriteMany (RWX)** - Multiple nodes read-write

---

## 3. Advanced Kubernetes Objects

### StatefulSet
- For stateful applications
- Provides stable network identities
- Ordered deployment and scaling

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql
spec:
  serviceName: mysql
  replicas: 3
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:8.0
        ports:
        - containerPort: 3306
        volumeMounts:
        - name: data
          mountPath: /var/lib/mysql
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: password
  volumeClaimTemplates:
  - metadata:
      name: data
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 10Gi
```

### DaemonSet
- Runs a pod on every node
- Use cases: logging, monitoring, networking

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd
spec:
  selector:
    matchLabels:
      app: fluentd
  template:
    metadata:
      labels:
        app: fluentd
    spec:
      containers:
      - name: fluentd
        image: fluentd:latest
        volumeMounts:
        - name: varlog
          mountPath: /var/log
      volumes:
      - name: varlog
        hostPath:
          path: /var/log
```

### Job
- Runs a task to completion
- Ensures pods successfully terminate

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: data-migration
spec:
  completions: 5        # Run 5 times
  parallelism: 2        # 2 pods at a time
  backoffLimit: 4       # Retry limit
  template:
    spec:
      containers:
      - name: migrator
        image: migration:latest
        command: ["python", "migrate.py"]
      restartPolicy: Never
```

### CronJob
- Scheduled jobs (like cron)

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: backup-job
spec:
  schedule: "0 2 * * *"  # Every day at 2 AM
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: backup
            image: backup:latest
            command: ["bash", "backup.sh"]
          restartPolicy: OnFailure
```

### Ingress
- Manages external access to services
- Provides HTTP/HTTPS routing
- Can provide SSL termination, name-based routing

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /api
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 8080
      - path: /web
        pathType: Prefix
        backend:
          service:
            name: web-service
            port:
              number: 80
  tls:
  - hosts:
    - myapp.example.com
    secretName: tls-secret
```

### HorizontalPodAutoscaler (HPA)
- Automatically scales pods based on metrics

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: nginx-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: nginx-deployment
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80
```

---

## 4. Essential Kubectl Commands

### Cluster Information
```bash
# Cluster info
kubectl cluster-info
kubectl version

# View nodes
kubectl get nodes
kubectl describe node <node-name>

# View all resources
kubectl get all
kubectl get all -A  # All namespaces
```

### Working with Pods
```bash
# Create pod from YAML
kubectl apply -f pod.yaml
kubectl create -f pod.yaml

# Get pods
kubectl get pods
kubectl get pods -o wide
kubectl get pods -n namespace-name
kubectl get pods --all-namespaces

# Describe pod (detailed info)
kubectl describe pod <pod-name>

# Delete pod
kubectl delete pod <pod-name>

# Execute command in pod
kubectl exec -it <pod-name> -- bash
kubectl exec <pod-name> -- ls /app

# View logs
kubectl logs <pod-name>
kubectl logs -f <pod-name>              # Follow logs
kubectl logs <pod-name> -c <container>  # Multi-container pod

# Copy files
kubectl cp <pod-name>:/path/file ./local-file
kubectl cp ./local-file <pod-name>:/path/

# Port forwarding
kubectl port-forward <pod-name> 8080:80
```

### Working with Deployments
```bash
# Create deployment
kubectl create deployment nginx --image=nginx:1.21
kubectl apply -f deployment.yaml

# Get deployments
kubectl get deployments
kubectl get deploy

# Scale deployment
kubectl scale deployment nginx --replicas=5

# Update image (rolling update)
kubectl set image deployment/nginx nginx=nginx:1.22

# View rollout status
kubectl rollout status deployment/nginx

# View rollout history
kubectl rollout history deployment/nginx

# Rollback to previous version
kubectl rollout undo deployment/nginx

# Rollback to specific revision
kubectl rollout undo deployment/nginx --to-revision=2

# Describe deployment
kubectl describe deployment nginx

# Delete deployment
kubectl delete deployment nginx
```

### Working with Services
```bash
# Create service
kubectl expose deployment nginx --port=80 --type=ClusterIP
kubectl apply -f service.yaml

# Get services
kubectl get services
kubectl get svc

# Describe service
kubectl describe service nginx

# Delete service
kubectl delete service nginx
```

### Working with Namespaces
```bash
# Create namespace
kubectl create namespace dev
kubectl apply -f namespace.yaml

# Get namespaces
kubectl get namespaces
kubectl get ns

# Set default namespace
kubectl config set-context --current --namespace=dev

# Delete namespace
kubectl delete namespace dev
```

### Working with ConfigMaps & Secrets
```bash
# Create ConfigMap
kubectl create configmap app-config --from-literal=key=value
kubectl create configmap app-config --from-file=config.properties
kubectl apply -f configmap.yaml

# Get ConfigMaps
kubectl get configmaps
kubectl get cm

# Describe ConfigMap
kubectl describe configmap app-config

# Create Secret
kubectl create secret generic db-secret --from-literal=password=secret123
kubectl create secret generic tls-secret --from-file=tls.crt --from-file=tls.key
kubectl apply -f secret.yaml

# Get Secrets
kubectl get secrets

# Describe Secret
kubectl describe secret db-secret

# Decode secret
kubectl get secret db-secret -o jsonpath='{.data.password}' | base64 -d
```

### Working with Labels & Selectors
```bash
# Add label to pod
kubectl label pod nginx-pod env=production

# Remove label
kubectl label pod nginx-pod env-

# Update label
kubectl label pod nginx-pod env=staging --overwrite

# Get pods by label
kubectl get pods -l env=production
kubectl get pods -l 'env in (production,staging)'

# Get all labeled resources
kubectl get all -l app=nginx
```

### Resource Management
```bash
# Get resource usage
kubectl top nodes
kubectl top pods

# Describe resource quotas
kubectl describe resourcequota

# Get events
kubectl get events
kubectl get events --sort-by='.lastTimestamp'

# API resources
kubectl api-resources
kubectl api-versions
```

### Debugging & Troubleshooting
```bash
# Check pod status
kubectl get pods
kubectl describe pod <pod-name>

# View logs
kubectl logs <pod-name>
kubectl logs <pod-name> --previous  # Previous container

# Execute commands
kubectl exec -it <pod-name> -- sh

# Debug with temporary pod
kubectl run debug --image=busybox -it --rm -- sh

# Check service endpoints
kubectl get endpoints

# Network debugging
kubectl run curl --image=curlimages/curl -it --rm -- sh

# Check DNS
kubectl exec -it <pod-name> -- nslookup kubernetes.default
```

### YAML Management
```bash
# Apply configuration
kubectl apply -f file.yaml
kubectl apply -f directory/
kubectl apply -f https://url/file.yaml

# Delete resources
kubectl delete -f file.yaml

# View YAML definition
kubectl get pod <pod-name> -o yaml
kubectl get deployment <name> -o yaml > deployment.yaml

# Dry run (test without creating)
kubectl apply -f file.yaml --dry-run=client
kubectl apply -f file.yaml --dry-run=server

# Explain resource fields
kubectl explain pod
kubectl explain pod.spec.containers
```

### Context & Configuration
```bash
# View current context
kubectl config current-context

# View all contexts
kubectl config get-contexts

# Switch context
kubectl config use-context <context-name>

# View config
kubectl config view

# Set namespace in context
kubectl config set-context --current --namespace=dev
```

---

## 5. Real-World Kubernetes Examples

### Complete MERN Stack Application

```yaml
# MongoDB StatefulSet
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mongodb
spec:
  serviceName: mongodb
  replicas: 1
  selector:
    matchLabels:
      app: mongodb
  template:
    metadata:
      labels:
        app: mongodb
    spec:
      containers:
      - name: mongodb
        image: mongo:6
        ports:
        - containerPort: 27017
        env:
        - name: MONGO_INITDB_ROOT_USERNAME
          valueFrom:
            secretKeyRef:
              name: mongo-secret
              key: username
        - name: MONGO_INITDB_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mongo-secret
              key: password
        volumeMounts:
        - name: mongo-data
          mountPath: /data/db
  volumeClaimTemplates:
  - metadata:
      name: mongo-data
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 10Gi

---
# MongoDB Service
apiVersion: v1
kind: Service
metadata:
  name: mongodb
spec:
  clusterIP: None  # Headless service for StatefulSet
  selector:
    app: mongodb
  ports:
  - port: 27017
    targetPort: 27017

---
# Backend Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend
    spec:
      containers:
      - name: backend
        image: myapp/backend:1.0
        ports:
        - containerPort: 5000
        env:
        - name: MONGODB_URI
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: mongodb_uri
        - name: JWT_SECRET
          valueFrom:
            secretKeyRef:
              name: app-secret
              key: jwt_secret
        resources:
          requests:
            memory: "256Mi"
            cpu: "250m"
          limits:
            memory: "512Mi"
            cpu: "500m"
        livenessProbe:
          httpGet:
            path: /health
            port: 5000
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /ready
            port: 5000
          initialDelaySeconds: 5
          periodSeconds: 5

---
# Backend Service
apiVersion: v1
kind: Service
metadata:
  name: backend
spec:
  type: ClusterIP
  selector:
    app: backend
  ports:
  - port: 5000
    targetPort: 5000

---
# Frontend Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - name: frontend
        image: myapp/frontend:1.0
        ports:
        - containerPort: 80
        env:
        - name: REACT_APP_API_URL
          value: "http://backend:5000"

---
# Frontend Service
apiVersion: v1
kind: Service
metadata:
  name: frontend
spec:
  type: LoadBalancer
  selector:
    app: frontend
  ports:
  - port: 80
    targetPort: 80

---
# ConfigMap
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  mongodb_uri: "mongodb://mongodb:27017/mydb"
  log_level: "info"

---
# Secret
apiVersion: v1
kind: Secret
metadata:
  name: mongo-secret
type: Opaque
stringData:
  username: admin
  password: strongpassword123
---
apiVersion: v1
kind: Secret
metadata:
  name: app-secret
type: Opaque
stringData:
  jwt_secret: your-secret-key-here

---
# Ingress
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: frontend
            port:
              number: 80
      - path: /api
        pathType: Prefix
        backend:
          service:
            name: backend
            port:
              number: 5000

---
# HorizontalPodAutoscaler for Backend
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: backend-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: backend
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
```

### Microservices with Service Mesh

```yaml
# API Gateway
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-gateway
spec:
  replicas: 2
  selector:
    matchLabels:
      app: api-gateway
  template:
    metadata:
      labels:
        app: api-gateway
    spec:
      containers:
      - name: gateway
        image: api-gateway:1.0
        ports:
        - containerPort: 8080

---
# User Service
apiVersion: apps/v1
kind: Deployment
metadata:
  name: user-service
spec:
  replicas: 3
  selector:
    matchLabels:
      app: user-service
  template:
    metadata:
      labels:
        app: user-service
    spec:
      containers:
      - name: user-service
        image: user-service:1.0
        ports:
        - containerPort: 8081
        env:
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: url

---
# Order Service
apiVersion: apps/v1
kind: Deployment
metadata:
  name: order-service
spec:
  replicas: 3
  selector:
    matchLabels:
      app: order-service
  template:
    metadata:
      labels:
        app: order-service
    spec:
      containers:
      - name: order-service
        image: order-service:1.0
        ports:
        - containerPort: 8082

---
# Services
apiVersion: v1
kind: Service
metadata:
  name: api-gateway
spec:
  type: LoadBalancer
  selector:
    app: api-gateway
  ports:
  - port: 80
    targetPort: 8080

---
apiVersion: v1
kind: Service
metadata:
  name: user-service
spec:
  selector:
    app: user-service
  ports:
  - port: 8081
    targetPort: 8081

---
apiVersion: v1
kind: Service
metadata:
  name: order-service
spec:
  selector:
    app: order-service
  ports:
  - port: 8082
    targetPort: 8082
```

---

## 6. Kubernetes Networking

### Service Types Comparison

| Type | Use Case | Accessibility |
|------|----------|---------------|
| ClusterIP | Internal communication | Cluster only |
| NodePort | Development/testing | External via NodeIP:Port |
| LoadBalancer | Production external access | External via LB IP |
| ExternalName | External service mapping | DNS CNAME |

### Network Policies

```yaml
# Allow only from specific pods
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: backend-policy
spec:
  podSelector:
    matchLabels:
      app: backend
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
    ports:
    - protocol: TCP
      port: 5000
  egress:
  - to:
    - podSelector:
        matchLabels:
          app: database
    ports:
    - protocol: TCP
      port: 5432
```

---

## 7. Kubernetes Security Best Practices

### Security Context

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secure-pod
spec:
  securityContext:
    runAsNonRoot: true
    runAsUser: 1000
    fsGroup: 2000
  containers:
  - name: app
    image: myapp:1.0
    securityContext:
      allowPrivilegeEscalation: false
      readOnlyRootFilesystem: true
      capabilities:
        drop:
        - ALL
```

### RBAC (Role-Based Access Control)

```yaml
# Role
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
  namespace: default
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]

---
# RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: default
subjects:
- kind: User
  name: jane
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

### Resource Quotas

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: dev
spec:
  hard:
    requests.cpu: "10"
    requests.memory: 20Gi
    limits.cpu: "20"
    limits.memory: 40Gi
    pods: "50"
```

### LimitRange

```yaml
