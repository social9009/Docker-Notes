🐳 Docker Mastery
Containers
Architecture
Dockerfile
Instructions
Volumes
Images
Multi-Stage
Compose
Networking
dockerignore
Registry
Projects
🐳
AWS DevOps Learning Series
Docker Mastery
Complete Reference
Scenario-driven Docker notes with real-time examples, architecture diagrams, and hands-on projects

Containers
Dockerfile
Volumes
Multi-Stage Builds
Networking
AWS DevOps
By Akshay Sawant · Pune
01
Containers & Containerization
📦
What is a Container?

A lightweight, standalone, executable package of software that includes everything needed to run a piece of software — code, runtime, system tools, libraries, and settings.
💡
Containers are isolated from each other and from the host system, ensuring consistent runs across different environments.
🔧
What is Containerization?

The packaging of software code with all its necessary components — libraries, frameworks, dependencies — so they are isolated in their own "container".
✅
Apps run quickly and reliably from one environment to another without reinstalling dependencies.
🏗️ Real-Time Scenario: You built a Java app on your laptop. Without Docker it works locally but fails in production because the server has a different Java version. With Docker — you package the exact Java version + your app in a container. Same container runs everywhere. ✅
🔄 Container Lifecycle Flow

Dockerfile
→
Docker Image
(has OS + packages)
→
Container
(running app)
→
Registry
(Docker Hub)
→
Deployment
⚠️
Key insight: Containers do NOT have an OS by default. That's why we use Images — images contain the OS layer + packages. You pull an image → create a container from it → deploy your app inside.
02
Docker Architecture
🖥️ Docker Client

The CLI tool you use to interact with Docker. Sends commands to the Docker Daemon via REST API.
docker build / run / ps
⚙️ Docker Host

The machine where Docker is installed. Contains the Daemon, Images, Containers & Networks.
🔁 Docker Daemon

Background service that manages all Docker objects — images, containers, volumes, networks.
📚 Docker Registry

Storage for Docker images. Default public registry is Docker Hub. Private: AWS ECR, GitHub Container Registry.
🐳 Docker vs Virtual Machine

🐳 Docker Container
App A
App B
Bins / Libs
Docker Engine
Host OS Kernel (Shared)
Hardware
VS
🖥️ Virtual Machine
App A
App B
Guest OS (Full)
Guest OS (Full)
Hypervisor
Hardware
FEATURE	DOCKER (CONTAINERS)	VIRTUAL MACHINES
OS	Shared Kernel	Separate Full OS
Startup Time	Seconds ⚡	Minutes 🐢
Size	Lightweight (MB)	Heavy (GB)
Performance	High	Medium
Isolation	Process-level	Full hardware-level
03
Understanding Dockerfile
A Dockerfile is a blueprint used to build Docker images automatically. Everything you write inside a Dockerfile becomes a part of the image. It uses DSL (Domain Specific Language) keywords (instructions + arguments).
📌
It can: choose base OS/environment · install dependencies · copy application code · configure env variables · expose ports · run startup commands.
Write
Dockerfile
→
docker build
(creates image)
→
docker run
(creates container)
→
App is Live
🚀
🔑 Important Commands Before Dockerfile

docker attach <ctr>
Connects to the main running process (PID 1) — use with caution
docker exec -it <ctr> /bin/bash
Creates a new process — safe way to work inside container ✅
docker ps -a | grep Exited
See only stopped containers
docker top <ctr>
Check PIDs of processes running inside a container
docker stats <ctr>
Shows CPU % and Memory % usage of container in real-time
docker commit <img> <ctr>
Create image from a running container (with all its files/config)
docker image prune
Delete all dangling (unused) images
docker cp /ec2/file.txt ctr:/app/
Copy file from EC2 → Container
04
Dockerfile Instructions Deep Dive
🔥
Layer-Creating Instructions (increase image size): FROM, RUN, COPY, ADD
Non-Layer-Creating: CMD, ENTRYPOINT, WORKDIR, EXPOSE, ENV, LABEL, USER, VOLUME, ARG
FROM — Foundation Layer

The first and mandatory instruction. Tells Docker which base image to start from. Every Dockerfile begins with FROM.
FROM ubuntu:22.04          # Linux base
FROM python:3.11          # Python runtime
FROM node:18-alpine       # Node.js minimal
FROM openjdk:17           # Java runtime
FROM alpine:3.19          # Ultra-minimal (~5MB)
⭐
Multi-Stage Power: You can use multiple FROM statements — Stage 1 builds, Stage 2 runs. This removes build tools from the final image, dramatically reducing size.
RUN — Execute Commands at Build Time

Executes commands during image build. Each RUN creates a new image layer. Used for installing packages, building apps, creating directories.
# ❌ Bad — multiple layers
RUN apt-get update
RUN apt-get install -y nginx

# ✅ Good — single layer (best practice)
RUN apt-get update && \
    apt-get install -y nginx curl git && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
Shell Form
RUN apt-get install -y nginx
Runs via /bin/sh -c
Exec Form
RUN ["apt-get", "install", "-y", "nginx"]
More secure, no shell involved ✅
CMD — Default Startup Command

Defines the default command that runs when a container starts. Only the last CMD in a Dockerfile is processed — writing multiple CMDs is useless. CMD can be overridden at runtime.
CMD ["nginx", "-g", "daemon off;"]    # Web server foreground
CMD ["java", "-jar", "/app.jar"]      # Java app
CMD ["npm", "start"]                  # Node.js
CMD ["bash", "-c", "echo 'hello' > /data/hello.txt"]
# ↑ Once task completes, container goes to EXITED state 🔥
COPY vs ADD

COPY: Copies files/dirs from local system (build context) into container image at build time.
ADD: Like COPY but with superpowers — can auto-extract tar files and download from URLs.
FEATURE	COPY	ADD
Simple file copy	✅	✅
Auto extract .tar	❌	✅
Download from URL	❌	✅
Recommended	✅ Prefer this	⚠️ Only when needed
🎯
Best practice for caching: Copy dependency file first, install deps, then copy rest of code. This prevents reinstalling deps on every code change.
COPY package.json .
RUN  npm install         # cached if package.json unchanged
COPY . .                 # only this re-runs on code changes
WORKDIR — Set Working Directory

Sets the working directory for all subsequent instructions (RUN, CMD, COPY, ADD, ENTRYPOINT). Creates the directory automatically if it doesn't exist.
WORKDIR /app    # creates /app if not exists, switches into it
COPY . .        # copies to /app
RUN ls          # runs inside /app
💡
Without WORKDIR, commands may run in / (root), causing wrong file paths or build failures. Always define WORKDIR early in Dockerfile.
LABEL
Adds metadata (key=value pairs) to the image. Used for versioning, ownership, CI/CD automation. Replaces deprecated MAINTAINER.
LABEL version="1.0" author="akshay"
no layer
EXPOSE
Documents which port the container listens on. Does NOT actually publish the port — use -p at runtime for that.
EXPOSE 8080/tcp
no layer
VOLUME
Creates a persistent mount point inside the container. Data stored here survives container restarts and deletions.
VOLUME ["/data"]
no layer
USER
Sets which user runs subsequent instructions. By default Docker runs as root — always switch to a non-root user for production security.
USER appuser
no layer
ENTRYPOINT
Defines the fixed main executable. Cannot be overridden (unlike CMD). Think: "this container IS this app."
ENTRYPOINT ["java","-jar","app.jar"]
no layer
ENV
Sets environment variables available during both build and runtime. Use for app config. Never store secrets here!
ENV NODE_ENV=production PORT=3000
no layer
ARG
Pass build-time variables to Dockerfile. Only available during the image build — NOT accessible in running container (unlike ENV).
ARG BUILD_VER=1.0
no layer
⚡ ENTRYPOINT + CMD Together (Interview Gold)

ENTRYPOINT ["python"]
CMD        ["app.py"]
# Docker runs: python app.py
Rule: ENTRYPOINT + CMD = FINAL COMMAND
🔒 ENTRYPOINT → fixed executable (can't override easily)
🔓 CMD → default arguments (easily overridden at runtime)
👤 USER — Non-Root Security Pattern

FROM node:18
WORKDIR /app
COPY . .
RUN npm install
RUN useradd -m nodeuser           # create non-root user
RUN chown -R nodeuser:nodeuser /app # give ownership
USER nodeuser                     # switch to non-root ✅
CMD ["node", "app.js"]
Why this matters: Prevents privilege escalation in Kubernetes/ECS. Reduces attack surface. Follows least privilege principle.
05
Docker Volumes — Persistent Storage
🔥
Core Problem: Containers are ephemeral. Container deleted → data lost. Volumes solve this by storing data OUTSIDE the container filesystem.
📦 Volume

Managed by Docker. Stored at /var/lib/docker/volumes/. Persistent across restarts and deletions. Best for production data.
✅ Production
🔗 Bind Mount

Maps a specific host directory to a container path. Bidirectional sync. Best for development and testing with local files.
⚡ Dev/Test
💨 tmpfs

Temporary in-memory storage. Data is lost when container stops. Use for sensitive/temporary data that shouldn't persist.
🔐 Sensitive
🔄 Volume Sharing Between Containers

cont-1
📦 volume1
/var/lib/docker/volumes/
cont-2
cont-3
(--volumes-from cont-2)
All 3 containers share the same volume — data is bidirectional and replicated
🔧 3 Methods to Create Volumes

Method 1 — Dockerfile

FROM ubuntu
VOLUME ["volume1"]
docker run -it --name cont-1 img
Method 2 — CLI Flag

docker run -it --name cont-4 \
  -v /volume2 ubuntu
docker run --volumes-from cont-4
Method 3 — AWS EBS

docker run -v /mnt/ebs:/data ...
Data persists even if EC2 terminates. Set Delete on Termination → NO.
⚠️
EBS Volume Setup: Create in same AZ as EC2 → Attach → mkfs.ext4 /dev/xvdb → mkdir /mnt/ebs → mount → add to /etc/fstab for persistence on reboot.
📂 Docker's Home Directory Structure

/var/lib/docker/ ├── volumes/ ← your named volumes live here │ └── 4eee37713e82.../ │ └── _data/ ← actual data files │ ├── file1.txt │ └── hello1.java ← bidirectional sync ⟺ ├── containers/ ├── image/ ├── overlay2/ └── network/
06
Docker Image Types
A Docker Image is an immutable, read-only template used to create containers. Uses a layered structure (OverlayFS) — each Dockerfile instruction creates a new layer. Layers are cached for faster builds.
Base Image (Ubuntu/Alpine)
↓
Dependencies Layer (npm install / pip install)
↓
Application Code Layer (COPY app/)
↓
Configuration Layer (ENV / EXPOSE)
↓
Container (thin writable layer on top)
🐳 The 5 Types of Base Images

Standard
python:3.11
300MB–1.6GB
Slim
python:3.11-slim
50–150MB
Alpine
:alpine
5–50MB
Distroless
distroless
2–30MB
Scratch
0 MB
TYPE	SIZE	SHELL	PKG MGR	DEBUG	USE CASE
🔵 Standard	300MB+	✅ bash	✅ apt	Easy	Development
🟡 Slim	50–150MB	✅ sh	✅ apt	Easy	Python/Node Production
🟢 Alpine	5–50MB	✅ sh	✅ apk	Medium	Go/Node Lightweight
🔴 Distroless	2–30MB	❌ None	❌ None	Hard	Secure Production
⬛ Scratch	0 MB	❌ None	❌ None	Impossible	Go/Rust Static Bins
🎯 Which Image for Which Language?

🐹 Go Application

Best: scratch > distroless > alpine
Go compiles to static binary — no OS deps needed.
🐍 Python Application

Best: python:3.11-slim
Avoid Alpine! Many Python packages use C extensions compiled against glibc — Alpine uses musl libc which causes crashes.
🟢 Node.js Application

Best: node:18-slim > node:18-alpine
Slim for npm packages needing native modules.
♨️ Java Application

Best: eclipse-temurin:21-jdk-slim or gcr.io/distroless/java21
JVM needs proper glibc — avoid Alpine for Java.
📊 Image Hierarchy

🪨 Base Image
FROM scratch — no parent
→
👶 Child Image
FROM python:3.11-slim
→
✅ Official Image
Verified by community
→
🏗️ App-Specific
YOUR custom image
07
Multi-Stage Dockerfile Builds
🚀
Why Multi-Stage? Instructions like FROM, RUN, COPY, ADD create layers and bloat image size. Build tools (Maven, Go compiler) are only needed during build — NOT in the final production image.
Compiled Languages
Java, Go, C/C++, Rust
Source code → compiled → binary
Requires build tools (Maven/Go compiler)
Interpreted Languages
Python, JavaScript, Ruby, PHP
Code runs directly via interpreter
No separate build stage needed
🏗️ Multi-Stage Architecture

🔨 STAGE 1: BUILD
Full compiler image (Maven / Go 1.21)
Source code + dependencies
Build tools (javac, go build)
Compile → produces artifact (.jar / binary)
~600MB–1.3GB — NEVER ships
→
only
artifact
crosses
🚀 STAGE 2: RUN
Minimal runtime (Alpine / JRE)
COPY --from=builder /app/output.jar .
No Maven, no compiler, no source code
Production-ready, secure, lean
~20MB–320MB ✅ Ships to prod
📏 Size Comparison — Go Application

Single-Stage
golang:1.21 (full)
1.29 GB ❌
Multi-Stage
21.9 MB ✅
Go Multi-Stage Dockerfile

# Stage 1: Build
FROM golang:1.21 AS builder
WORKDIR /app
COPY . .
RUN CGO_ENABLED=0 GOOS=linux go build -o hello main.go

# Stage 2: Run (Alpine minimal)
FROM alpine:3.19
WORKDIR /app
COPY --from=builder /app/hello .   # only the binary crosses over
EXPOSE 8080
CMD ["./hello"]
Java Spring Boot Multi-Stage (Apple M1/M2/M3)

# Stage 1: Build (Maven compiles .java → .jar)
FROM maven:3.9.3-amazoncorretto-17 AS builder
WORKDIR /app
COPY pom.xml .
RUN mvn dependency:go-offline -B   # pre-download deps (cached layer)
COPY src ./src
RUN mvn clean package -DskipTests  # produces .jar in target/

# Stage 2: Run (minimal Alpine JRE)
FROM amazoncorretto:17-alpine
WORKDIR /app
COPY --from=builder /app/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
⚠️
Apple M1/M2/M3: Use amazoncorretto-17 instead of openjdk-17 — OpenJDK images may not have ARM64 variants on Docker Hub.
⚡ Resource Limits

docker stats <containerID>
See CPU % and Memory % live
docker run --cpus="0.1" --memory="100MB" ubuntu
Limit CPU and memory at creation
docker update <name> --cpus="0.5" --memory="200MB"
Update limits on running container
⚠️
Memory limits can only be decreased after setting — not increased. For advanced resource management use Kubernetes Resource Quotas.
08
Docker Compose
Docker Compose lets you define and run multi-container applications using a single docker-compose.yml file. Perfect for apps with frontend, backend, database, and cache running together.
# docker-compose.yml structure
version: "3.9"
services:
  backend:
    build: ./backend            # path to Dockerfile
    image: my-backend:v1
    container_name: backend-ctr
    ports:
      - "5000:5000"
    networks:
      - app-net
  db:
    image: mysql:8.0
    volumes:
      - db_data:/var/lib/mysql
    networks:
      - app-net
volumes:
  db_data:
networks:
  app-net:
COMMAND	BUILDS IMAGE?	REFLECTS CODE CHANGES?	SPEED	USE WHEN
docker compose up -d	❌ No	❌ No	⚡ Fast	No changes made
docker compose up -d --build	✅ Yes	✅ Yes	🐢 Slower	Code/Dockerfile changed
09
Docker Networking
🌐
Docker networking enables communication between multiple containers running on the same host or different Docker hosts. Networks are isolated — containers on different networks cannot talk unless configured.
1️⃣ Bridge Network (Default)

Used when 2 containers on the same host need to communicate. Docker creates a virtual bridge interface (docker0). Each container gets its own IP.
docker network create mynet
docker run --network=mynet app1
2️⃣ Host Network

Container uses the host machine's network directly (EC2's VPC). No port mapping needed. Container ports become host ports.
docker run --network=host app
3️⃣ None Network

Container has no network access at all. Completely isolated — use when you don't want to expose any application.
docker run --network=none app
4️⃣ Overlay Network

For containers on different Docker hosts. Used with Docker Swarm and Kubernetes. Creates a distributed network across multiple machines.
docker network create --driver overlay myswarm
5️⃣ Macvlan Network

Attaches containers directly to the physical network. Gives containers their own MAC addresses on the physical network for advanced use cases.
🔗 Microservices Network Flow

Frontend
:3000
Backend
:5000
Database
:5432
Redis
:6379
User-Defined Bridge Network (app-network)
Docker Host (EC2)
On a user-defined bridge network, containers use container names as DNS hostnames — no need for IPs. Frontend calls http://backend:5000 directly.
10
.dockerignore File
🔥
When you run docker build ., Docker sends the ENTIRE directory (build context) to the Docker daemon — including node_modules, .git, logs, and secrets. Without .dockerignore this causes: huge build context, slow CI/CD, and security leaks!
❌ Without .dockerignore

Sending build context to Docker daemon
850 MB
✗ Slow CI/CD pipeline
✗ .env secrets leaked into image
✗ node_modules copied unnecessarily
✗ Cache invalidated on every change
✅ With .dockerignore

Sending build context to Docker daemon
45 MB
✓ Fast builds
✓ Secure image (no secrets)
✓ Efficient layer caching
✓ Reduced infrastructure cost
# .dockerignore — Best Practice Template

# Git files
.git
.gitignore

# Dependencies (rebuilt inside container)
node_modules
vendor

# Secrets (CRITICAL — never bake these in!)
.env
*.pem
secrets/

# Logs and temp
*.log
logs/
tmp/

# OS artifacts
.DS_Store
Thumbs.db

# Build artifacts
dist/
build/
target/

# Advanced: ignore everything EXCEPT these files
*
!app.py
!requirements.txt
FEATURE	.DOCKERIGNORE	.GITIGNORE
Scope	Docker build context	Git repository
Purpose	Optimize builds + security	Version control
Affects image	Yes	No
Security role	High 🔐	Medium
11
Private Registry & Push/Pull
Developer
Code Push
→
CI/CD
docker build
→
docker tag
& push
→
Private Registry
(ECR / Hub)
→
K8s/ECS
pulls & deploys
🐳 Docker Hub Private Repo

1️⃣
Create private repo on Docker Hub
docker login
Authenticate with PAT (Personal Access Token)
docker tag myapp:latest user/myapp:v1
Tag with repo format
docker push user/myapp:v1
Push to private registry
☁️ AWS ECR (Industry Standard)

aws ecr get-login-password \
  --region ap-south-1 | \
docker login --username AWS \
  --password-stdin \
  <account-id>.dkr.ecr.ap-south-1.amazonaws.com

docker tag myapp:latest \
  <account-id>.dkr.ecr.ap-south-1.amazonaws.com/myapp:v1

docker push \
  <account-id>.dkr.ecr.ap-south-1.amazonaws.com/myapp:v1
🔑 PAT Permission Levels

PERMISSION	CAN PULL PUBLIC	CAN PULL PRIVATE	CAN PUSH	CAN DELETE
Public read-only	✅	❌	❌	❌
Read-only	✅	✅	❌	❌
Read & Write	✅	✅	✅	❌
Read, Write & Delete	✅	✅	✅	✅
🔐
Security: Never hardcode passwords in scripts. Use DOCKER_PASSWORD as GitHub Secret. Never push images with .env secrets baked in — always use .dockerignore.
🔄 Mutable Container

Container where you can make changes directly inside while it's running. Used for development and debugging. Changes don't persist after deletion.
🔒 Immutable Container

Container where configuration/files cannot be changed once running. Production standard — ensures consistency across environments.
⚡ Foreground vs Background Mode

MODE	FLAG	TERMINAL	I/O STREAMS	USE CASE
Foreground	-it	Attached	Direct STDIN/STDOUT	Dev/debugging
Background	-d	Detached	Via docker logs/exec	Production
💡
Key insight: The core concept isn't just "interaction" — it's process attachment. Foreground = attached process, Background = detached process. Kubernetes always runs containers detached (no terminal).
12
Hands-On Projects
PROJECT 1
🌐 Portfolio Website Deployment
nginx:alpine
Containerize a static portfolio website using nginx:alpine as base image. Deploy on EC2 via Docker.

FROM nginx:alpine
COPY . /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]

docker build -t akshay-portfolio .
docker run -d -p 80:80 --name portfolio akshay-portfolio
PROJECT 2
🐍 Python Image Type Comparison
Standard | Slim | Alpine | Distroless
Same Flask app built with 4 different base images. Demonstrates 94% size reduction by switching from Standard → Alpine.

py-standard
1.62 GB
Baseline
py.slim
236MB
↓85%
py.alpine
88MB
↓94% ✅
py.distroless
223MB
↓86%
PROJECT 3 & 4
🐹 Go + ♨️ Java Multi-Stage Docker
Multi-Stage Build
Go app: 1.29GB → 21.9MB (98% reduction!). Java Spring Boot app: ~700MB → 320MB (54% reduction). Both use multi-stage builds.

APP	SINGLE-STAGE	MULTI-STAGE	REDUCTION
Go App	1.29 GB	21.9 MB	98% 🔥
Java Spring Boot	~700 MB	~320 MB	54% ✅
Java .war (Tomcat)	~800 MB	~350 MB	~56% ✅
PROJECT 5
🏠 Casa Royale — Luxury PG Marketing Website
Spring Boot + Multi-Stage Docker
Full luxury marketing website for Casa Royale PG (Hinjewadi, Pune). Built with Java Spring Boot. Containerized using multi-stage Docker build. Features 33+ amenities showcase, video tour, contact section.

Spring Boot Static Serving
src/main/resources/static/ → served at root URL automatically
Smart Layer Caching
Copy pom.xml first → install deps (cached) → copy src/ → build
PROJECT 6
🎬 CinemaWorld — 5 Microservices | Docker Compose | AWS
Docker Compose | Microservices
Full industry-level microservices deployment with Docker Compose. Frontend + Backend + Database + Cache + Message Queue. All services communicate via a user-defined bridge network.

13
⚡ Quick Cheat Sheet
Container Commands

docker run -d -p 80:80 img
Background + port map
docker run -it img /bin/bash
Interactive shell
docker ps
Running containers
docker ps -a
All containers
docker stop / rm <ctr>
Stop / delete
docker logs -f <ctr>
Stream logs
docker inspect <ctr>
Full container info
Image Commands

docker build -t name:v1 .
Build from Dockerfile
docker images
List images
docker pull ubuntu:22.04
Pull from registry
docker push user/img:v1
Push to registry
docker rmi <img>
Remove image
docker image prune
Remove dangling images
docker commit <ctr> img
Image from container
Volume Commands

docker volume ls
List volumes
docker volume create vol1
Create named volume
docker volume inspect vol1
Inspect volume
docker run -v vol:/path img
Attach named volume
docker run -v /host:/ctr img
Bind mount
--volumes-from cont-1
Share volumes
docker inspect ctr | grep Volume
Check attached volumes
Network Commands

docker network ls
List networks
docker network create mynet
Create bridge network
docker network inspect mynet
Inspect network
docker run --network mynet img
Attach to network
docker network connect mynet ctr
Connect running container
docker network rm mynet
Remove network
14
🛠️ Docker Installation on Ubuntu
# 1. Update and install dependencies
sudo apt update && sudo apt install -y ca-certificates curl gnupg lsb-release

# 2. Add Docker's official GPG key
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
  sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# 3. Set up Docker repository
echo "deb [arch=$(dpkg --print-architecture) \
  signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 4. Install Docker Engine
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io \
  docker-buildx-plugin docker-compose-plugin

# 5. Post-install: run without sudo
sudo usermod -aG docker $USER && newgrp docker

# 6. Enable at boot + verify
sudo systemctl enable docker && sudo systemctl start docker
docker --version && docker run hello-world
⚠️
Why NOT apt install docker.io? Ubuntu's repository version is outdated and missing latest features like BuildKit, Compose V2, and security patches. Always install from Docker's official repository.
🔗 Jenkins + Docker Integration

For Jenkins to run Docker commands, Jenkins user needs access to the Docker socket:

sudo usermod -aG docker jenkins
Add Jenkins to docker group (MOST IMPORTANT)
sudo systemctl restart jenkins
Apply group change
sudo su - jenkins && docker ps
Verify Docker access from Jenkins user
Akshay Sawant
AWS DevOps Engineer · AWS Solutions Architect Associate · Pune, Maharashtra
Docker
AWS
DevOps
Kubernetes
CI/CD
⭐ If this helped you, give the repo a star! ⭐
Part of the DevOps Learning Series · Scenario-based Docker Notes# Docker-Notes
