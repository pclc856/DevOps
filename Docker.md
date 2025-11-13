# Docker & YAML Complete Guide for DevOps Interview

## 1. Docker Basics

### What is Docker?
- **Containerization platform** that packages applications with dependencies
- **Lightweight** compared to VMs (shares host OS kernel)
- **Portable** - runs consistently across environments
- **Isolated** - each container runs independently

### Docker Architecture
```
┌─────────────────────────────────────┐
│        Docker Client (CLI)          │
└──────────────┬──────────────────────┘
               │
┌──────────────▼──────────────────────┐
│         Docker Daemon                │
│  ┌────────────────────────────────┐ │
│  │      Container Runtime         │ │
│  └────────────────────────────────┘ │
└─────────────────────────────────────┘
               │
┌──────────────▼──────────────────────┐
│        Docker Registry (Hub)         │
└─────────────────────────────────────┘
```

**Key Components:**
- **Docker Daemon**: Background service managing containers
- **Docker Client**: CLI interface (docker command)
- **Docker Images**: Read-only templates
- **Docker Containers**: Running instances of images
- **Docker Registry**: Storage for images (Docker Hub)

### Essential Docker Commands

#### Image Management
```bash
# Pull image from registry
docker pull nginx:latest

# List images
docker images
docker image ls

# Remove image
docker rmi nginx:latest
docker image rm <image-id>

# Build image from Dockerfile
docker build -t myapp:1.0 .

# Tag image
docker tag myapp:1.0 username/myapp:1.0

# Push to registry
docker push username/myapp:1.0

# Inspect image
docker image inspect nginx

# Show image history
docker history nginx
```

#### Container Management
```bash
# Run container
docker run -d --name mycontainer nginx
docker run -it ubuntu bash  # Interactive mode

# Run with port mapping
docker run -d -p 8080:80 nginx

# Run with volume mount
docker run -d -v /host/path:/container/path nginx

# Run with environment variables
docker run -d -e ENV_VAR=value nginx

# List containers
docker ps           # Running containers
docker ps -a        # All containers

# Stop/Start container
docker stop mycontainer
docker start mycontainer
docker restart mycontainer

# Remove container
docker rm mycontainer
docker rm -f mycontainer  # Force remove

# Execute command in running container
docker exec -it mycontainer bash

# View logs
docker logs mycontainer
docker logs -f mycontainer  # Follow logs

# Copy files
docker cp mycontainer:/path/file /host/path

# Inspect container
docker inspect mycontainer

# View resource usage
docker stats
```

#### System Commands
```bash
# Clean up
docker system prune     # Remove unused data
docker system prune -a  # Remove all unused images

# View disk usage
docker system df

# Remove all stopped containers
docker container prune

# Remove unused volumes
docker volume prune

# Remove unused networks
docker network prune
```

---

## 2. Dockerfile - Creating Custom Images

### Dockerfile Structure
```dockerfile
# Base image
FROM ubuntu:22.04

# Metadata
LABEL maintainer="your-email@example.com"
LABEL version="1.0"
LABEL description="My application"

# Set environment variables
ENV APP_HOME=/app
ENV PORT=8080

# Set working directory
WORKDIR /app

# Copy files
COPY . /app
COPY package.json /app/

# Run commands during build
RUN apt-get update && \
    apt-get install -y python3 && \
    apt-get clean

# Install dependencies
RUN pip install -r requirements.txt

# Expose port
EXPOSE 8080

# Define volume mount point
VOLUME ["/app/data"]

# Set user
USER appuser

# Health check
HEALTHCHECK --interval=30s --timeout=3s \
  CMD curl -f http://localhost:8080/health || exit 1

# Default command
CMD ["python3", "app.py"]
# OR
ENTRYPOINT ["python3"]
CMD ["app.py"]
```

### Common Dockerfile Instructions

| Instruction | Purpose | Example |
|------------|---------|---------|
| `FROM` | Base image | `FROM python:3.9` |
| `RUN` | Execute command during build | `RUN apt-get update` |
| `COPY` | Copy files from host | `COPY . /app` |
| `ADD` | Copy + extract archives | `ADD file.tar.gz /app` |
| `WORKDIR` | Set working directory | `WORKDIR /app` |
| `ENV` | Set environment variable | `ENV DEBUG=true` |
| `EXPOSE` | Document port | `EXPOSE 8080` |
| `CMD` | Default command (overridable) | `CMD ["npm", "start"]` |
| `ENTRYPOINT` | Fixed command | `ENTRYPOINT ["python"]` |
| `USER` | Set user | `USER appuser` |
| `ARG` | Build-time variable | `ARG VERSION=1.0` |
| `VOLUME` | Mount point | `VOLUME /data` |

### Real-World Dockerfile Examples

#### Python Flask Application
```dockerfile
FROM python:3.9-slim

WORKDIR /app

# Copy requirements first (layer caching)
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy application code
COPY . .

# Non-root user
RUN useradd -m appuser && chown -R appuser:appuser /app
USER appuser

EXPOSE 5000

CMD ["python", "app.py"]
```

#### Node.js Application
```dockerfile
FROM node:18-alpine

WORKDIR /usr/src/app

# Install dependencies
COPY package*.json ./
RUN npm ci --only=production

# Copy source code
COPY . .

# Build if needed
RUN npm run build

EXPOSE 3000

CMD ["node", "server.js"]
```

#### Multi-Stage Build (Optimized)
```dockerfile
# Stage 1: Build
FROM node:18 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Stage 2: Production
FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
EXPOSE 3000
CMD ["node", "dist/server.js"]
```

---

## 3. Docker Compose (YAML)

### What is Docker Compose?
- Tool for defining **multi-container** applications
- Uses **YAML** configuration files
- Single command to start/stop entire stack

### Docker Compose File Structure

#### Basic Example
```yaml
version: '3.8'

services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
    volumes:
      - ./html:/usr/share/nginx/html
    environment:
      - NGINX_HOST=localhost
    networks:
      - frontend

  database:
    image: postgres:14
    environment:
      POSTGRES_DB: mydb
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
    volumes:
      - db-data:/var/lib/postgresql/data
    networks:
      - backend

networks:
  frontend:
  backend:

volumes:
  db-data:
```

### Complete Docker Compose Reference

#### Service Configuration
```yaml
services:
  myapp:
    # Image
    image: nginx:latest
    
    # OR Build from Dockerfile
    build:
      context: ./app
      dockerfile: Dockerfile
      args:
        VERSION: "1.0"
    
    # Container name
    container_name: my-app-container
    
    # Port mapping
    ports:
      - "8080:80"          # host:container
      - "443:443"
    
    # Volume mounting
    volumes:
      - ./data:/app/data   # Bind mount
      - db-data:/var/lib   # Named volume
      - /tmp               # Anonymous volume
    
    # Environment variables
    environment:
      - DB_HOST=database
      - DEBUG=true
      NODE_ENV: production
    
    # Load from .env file
    env_file:
      - .env
      - .env.local
    
    # Dependencies
    depends_on:
      - database
      - redis
    
    # Restart policy
    restart: always  # no, on-failure, unless-stopped
    
    # Network configuration
    networks:
      - frontend
      - backend
    
    # Command override
    command: ["python", "app.py"]
    
    # Entrypoint override
    entrypoint: /docker-entrypoint.sh
    
    # Health check
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
    
    # Resource limits
    deploy:
      resources:
        limits:
          cpus: '0.5'
          memory: 512M
        reservations:
          cpus: '0.25'
          memory: 256M
```

### Real-World Docker Compose Examples

#### Full Stack Application (MERN)
```yaml
version: '3.8'

services:
  # MongoDB Database
  mongodb:
    image: mongo:6
    container_name: mongodb
    restart: always
    environment:
      MONGO_INITDB_ROOT_USERNAME: admin
      MONGO_INITDB_ROOT_PASSWORD: password
    ports:
      - "27017:27017"
    volumes:
      - mongo-data:/data/db
    networks:
      - mern-network

  # Backend API (Node.js/Express)
  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    container_name: backend
    restart: always
    ports:
      - "5000:5000"
    environment:
      NODE_ENV: production
      MONGODB_URI: mongodb://admin:password@mongodb:27017/mydb?authSource=admin
      JWT_SECRET: your-secret-key
    depends_on:
      - mongodb
    networks:
      - mern-network
    volumes:
      - ./backend:/app
      - /app/node_modules

  # Frontend (React)
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    container_name: frontend
    restart: always
    ports:
      - "3000:80"
    environment:
      REACT_APP_API_URL: http://localhost:5000
    depends_on:
      - backend
    networks:
      - mern-network

volumes:
  mongo-data:

networks:
  mern-network:
    driver: bridge
```

#### WordPress with MySQL
```yaml
version: '3.8'

services:
  mysql:
    image: mysql:8.0
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wpuser
      MYSQL_PASSWORD: wppassword
    volumes:
      - mysql-data:/var/lib/mysql
    networks:
      - wp-network

  wordpress:
    image: wordpress:latest
    restart: always
    ports:
      - "8080:80"
    environment:
      WORDPRESS_DB_HOST: mysql:3306
      WORDPRESS_DB_USER: wpuser
      WORDPRESS_DB_PASSWORD: wppassword
      WORDPRESS_DB_NAME: wordpress
    volumes:
      - wordpress-data:/var/www/html
    depends_on:
      - mysql
    networks:
      - wp-network

volumes:
  mysql-data:
  wordpress-data:

networks:
  wp-network:
```

#### Microservices with Nginx Load Balancer
```yaml
version: '3.8'

services:
  # Nginx Load Balancer
  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
    depends_on:
      - api1
      - api2
    networks:
      - app-network

  # API Service 1
  api1:
    build: ./api
    environment:
      - SERVICE_NAME=api1
    networks:
      - app-network

  # API Service 2
  api2:
    build: ./api
    environment:
      - SERVICE_NAME=api2
    networks:
      - app-network

  # Redis Cache
  redis:
    image: redis:alpine
    ports:
      - "6379:6379"
    networks:
      - app-network

  # PostgreSQL Database
  postgres:
    image: postgres:14
    environment:
      POSTGRES_DB: appdb
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: password
    volumes:
      - postgres-data:/var/lib/postgresql/data
    networks:
      - app-network

volumes:
  postgres-data:

networks:
  app-network:
    driver: bridge
```

### Docker Compose Commands

```bash
# Start services
docker-compose up
docker-compose up -d           # Detached mode
docker-compose up --build      # Rebuild images

# Stop services
docker-compose down
docker-compose down -v         # Remove volumes too

# View logs
docker-compose logs
docker-compose logs -f service_name

# List services
docker-compose ps

# Execute command
docker-compose exec service_name bash

# Scale services
docker-compose up -d --scale api=3

# Build images
docker-compose build

# Pull images
docker-compose pull

# Restart services
docker-compose restart
```

---

## 4. Docker Networking

### Network Types

```bash
# List networks
docker network ls

# Create network
docker network create my-network

# Inspect network
docker network inspect bridge

# Connect container to network
docker network connect my-network container1

# Disconnect
docker network disconnect my-network container1
```

### Network Modes

| Mode | Description | Use Case |
|------|-------------|----------|
| `bridge` | Default, isolated network | Multi-container apps |
| `host` | Use host network | Performance critical |
| `none` | No networking | Security isolation |
| `overlay` | Multi-host networking | Docker Swarm |
| `macvlan` | Assign MAC address | Legacy apps |

---

## 5. Docker Volumes

### Volume Types

```bash
# Named volumes
docker volume create my-volume
docker run -v my-volume:/data nginx

# Bind mounts
docker run -v /host/path:/container/path nginx

# tmpfs mounts (in-memory)
docker run --tmpfs /app/cache nginx
```

### Volume Commands
```bash
# List volumes
docker volume ls

# Inspect volume
docker volume inspect my-volume

# Remove volume
docker volume rm my-volume

# Prune unused volumes
docker volume prune
```

---

## 6. Advanced Docker Topics

### Multi-Stage Builds
**Benefits**: Smaller images, build-time vs runtime dependencies

```dockerfile
# Build stage
FROM golang:1.20 AS builder
WORKDIR /app
COPY . .
RUN go build -o main .

# Production stage
FROM alpine:latest
COPY --from=builder /app/main /main
CMD ["/main"]
```

### Docker Security Best Practices

1. **Use official base images**
```dockerfile
FROM node:18-alpine  # Official + minimal
```

2. **Run as non-root user**
```dockerfile
RUN adduser -D appuser
USER appuser
```

3. **Scan images for vulnerabilities**
```bash
docker scan myapp:latest
```

4. **Minimize layers**
```dockerfile
# Bad: Multiple layers
RUN apt-get update
RUN apt-get install -y curl
RUN apt-get clean

# Good: Single layer
RUN apt-get update && \
    apt-get install -y curl && \
    apt-get clean
```

5. **Use .dockerignore**
```
node_modules
.git
*.md
.env
```

### Docker Registry

```bash
# Login to registry
docker login

# Tag for private registry
docker tag myapp:latest registry.example.com/myapp:latest

# Push to private registry
docker push registry.example.com/myapp:latest

# Pull from private registry
docker pull registry.example.com/myapp:latest
```

### Docker Swarm (Orchestration)

```bash
# Initialize swarm
docker swarm init

# Deploy stack
docker stack deploy -c docker-compose.yml myapp

# List services
docker service ls

# Scale service
docker service scale myapp_web=5

# Remove stack
docker stack rm myapp
```

---

## 7. YAML Basics

### YAML Syntax Rules

```yaml
# Key-value pairs
name: John Doe
age: 30

# Lists
fruits:
  - apple
  - banana
  - orange

# OR inline
fruits: [apple, banana, orange]

# Nested objects
person:
  name: John
  address:
    city: New York
    zip: 10001

# Multi-line strings
description: |
  This is a multi-line
  string that preserves
  line breaks

description: >
  This is a multi-line
  string that folds into
  a single line

# Comments
# This is a comment

# Boolean
enabled: true
disabled: false

# Null
value: null
value: ~

# Anchors and Aliases (reuse)
defaults: &defaults
  adapter: postgres
  host: localhost

development:
  <<: *defaults
  database: dev_db

production:
  <<: *defaults
  database: prod_db
```

---

## 8. Interview Questions & Answers

### Basic Questions

**Q: What is the difference between Docker image and container?**
- Image: Read-only template with application code and dependencies
- Container: Running instance of an image with writable layer

**Q: Dockerfile vs Docker Compose?**
- Dockerfile: Builds a single image
- Docker Compose: Orchestrates multiple containers

**Q: What is the difference between CMD and ENTRYPOINT?**
- CMD: Default command, can be overridden
- ENTRYPOINT: Fixed command, arguments can be appended

**Q: What are Docker layers?**
- Each Dockerfile instruction creates a layer
- Layers are cached for faster builds
- Only changed layers are rebuilt

**Q: How to reduce Docker image size?**
- Use multi-stage builds
- Use alpine-based images
- Combine RUN commands
- Remove unnecessary files
- Use .dockerignore

### Intermediate Questions

**Q: Explain Docker networking modes**
- Bridge: Default isolated network
- Host: Share host network stack
- None: No networking
- Overlay: Multi-host communication

**Q: What is Docker volume and why use it?**
- Persistent data storage outside container
- Survives container deletion
- Can be shared between containers

**Q: How to share data between containers?**
- Use Docker volumes
- Use bind mounts
- Use named volumes in docker-compose

**Q: What is container orchestration?**
- Managing multiple containers across hosts
- Tools: Docker Swarm, Kubernetes
- Features: Scaling, load balancing, self-healing

### Advanced Questions

**Q: How to debug a failing container?**
```bash
docker logs container_name
docker exec -it container_name sh
docker inspect container_name
docker events
```

**Q: What is Docker layer caching?**
- Each layer is cached after first build
- Unchanged layers are reused
- Order matters: put frequently changing files last

**Q: Explain multi-stage builds**
- Use multiple FROM statements
- Copy artifacts between stages
- Keeps final image small

**Q: How to pass secrets securely?**
- Use environment variables (not for sensitive data)
- Docker secrets (Swarm mode)
- External secret managers (HashiCorp Vault)
- Build-time secrets with BuildKit

---

## 9. Hands-On Practice Tasks

### Task 1: Simple Web Application
```bash
# Create Dockerfile for Python Flask app
# Create docker-compose.yml with Flask + Redis
# Deploy and test
```

### Task 2: Multi-Container App
```bash
# Set up Node.js API + PostgreSQL + Nginx
# Configure networking
# Implement health checks
```

### Task 3: CI/CD Pipeline
```bash
# Create Dockerfile with multi-stage build
# Write docker-compose for testing
# Push to Docker Hub
```

---

## 10. Common Issues & Troubleshooting

| Issue | Solution |
|-------|----------|
| Port already in use | Change port mapping or stop conflicting service |
| Permission denied | Run with `sudo` or add user to docker group |
| Image not found | Check image name/tag, ensure `docker pull` |
| Container exits immediately | Check logs with `docker logs` |
| Network connectivity | Verify network configuration and firewall |
| Out of disk space | Run `docker system prune -a` |

---

## Quick Reference Cheat Sheet

```bash
# Essential Commands
docker build -t name:tag .
docker run -d -p 8080:80 --name container nginx
docker ps -a
docker logs -f container
docker exec -it container bash
docker-compose up -d
docker-compose down -v
docker system prune -a

# Debugging
docker inspect container
docker stats
docker events
docker top container

# Registry
docker login
docker push username/image:tag
docker pull username/image:tag
```

---

## Resources for Practice

- **Docker Playground**: play-with-docker.com
- **Docker Hub**: hub.docker.com
- **Official Docs**: docs.docker.com
- **Practice Labs**: katacoda.com/courses/docker

Good luck with your DevOps interview! 🚀
