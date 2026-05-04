
Containers: A containers in docker is a lightweight ,standalone ,and executable package of sotfware that includes everythings, needed to run a piece of software , including the code, runtime, system tools, libraries, and settings.
- Containers are isolated from one other and form the host system, ensuring, that they run consistently, across different environment.

Containerzation: It is the packaging of software code with all its necessary components like libraries, framework, and other dependencies so that they are isolated in their own "container".
-Inside containers, application will be deployed.
-Containers will not have OS by Default.
- If OS is not there , we can install package
- hence we can not deploy application.
- For this purpose we will use image.
- Inside docker image we will have OS and package.ex, if you want to deploy an app in container first we need to download the image. Image contains OS and package, From image we will create containers
- Inside containers we will deploy applications.
-Conatinerization enables the aps, to run quickly and reliably from one environment to other without the need to install and configure dependencies separately.


Docker Architecture:
🧠 Docker vs VM Architecture (Important for Interviews)
| Feature      | Docker (Containers) | Virtual Machines |
| ------------ | ------------------- | ---------------- |
| OS           | Shared Kernel       | Separate OS      |
| Startup Time | Seconds             | Minutes          |
| Size         | Lightweight         | Heavy            |
| Performance  | High                | Medium           |

🔥 Real-Time DevOps Use Case
Scenario:
You built a Java app → want consistent deployment
✔ Without Docker:
Works on dev, fails on prod ❌
✔ With Docker:
Same container everywhere ✅
🧩 Summary (Interview Ready)
Docker uses client-server architecture
Main components:
Client → sends command
Daemon → executes
Images → templates
Containers → running apps
Registry → storage
Uses namespaces + cgroups for isolation
Faster and lighter than VMs


Docker Client- Using this we will interact with docker.
Docker Host- Host is where we will install docker.
Daemon- manages all docker components like images, containers, volumes.
docker Hub- Manages all docker images . Also known as Registery.

❌ Docker attach- It will help you to connect with main process that is running inside the container
✅ Docker exec- It will help you to connect with the new process where we can safely work.

🔥 To see only the stopped containers--> docker ps -a | grep Exited

🔥 docker top - To check the process running inside the container PID.

🔥 docker stat - It will show how much % of CPU, memory container using.

🔥 OS Level Virtualization: The processe of taking the backup fo OS ,is known as OS level Virtualization.

🔥 If u want to create a image of existing  running containers command is--->  docker commit <imagename> <containername>
   what ever the files, configuration , data that is available in existing container that will be available in new images.

🔥 In real time we will write Dockerfile we are configure everything that we are going to install. The purpose of 
dockerfile to to do image automation .

🔥 To delete all unsed images (dangling images)---> docker image prune

🔥 copying the file from Ec2 to container in specific location 

1- 🔥 Method 1: Using docker cp (Most Common)
📌 Syntax
docker cp <source_path_on_ec2> <container_id>:<destination_path_in_container>

🔍 Method 2: Copy Folder Instead of File
docker cp /home/ec2-user/myfolder my-container:/app/
✔ Copies entire directory recursively

🔥 Method 3: Using Container ID (Alternative)
docker cp /home/ec2-user/test.txt a1b2c3d4e5f6:/tmp/
🔄 Reverse (Container → EC2)
docker cp my-container:/app/data/test.txt /home/ec2-user/


-------------------------------------------
Understanding Dockerfile
-------------------------------------------
A dockerfile is a blueprint which is used to build docker images automatically.
whatever you write inside a Dockerfile become a part of the image that we are going to created. 
A dockerfile contains instructions + arguments that: choose the base OS / environment , install dependencies, copy application code form one location to any location, configure the environment variables, expose ports, run startup commands,

Process: Dockerfile-----> Docker Image-------> Docker container (App)
In dockerfile we will use DSL(Domain Specific Language) Keywords.
--------------------------------------------------------------------------------------------------------
🔥 FROM - The FROM instruction is the foundation layer of any Docker image. It tells Docker:
“Which base image should I start building my container from?”
--------------------------------------------------------------------------------------------------------
🧱 Basic Syntax
FROM <image_name>:<tag>
✅ Example
FROM ubuntu:22.04

🧪 Common Base Images
🐧 Linux-based
FROM ubuntu:22.04
FROM centos:7
FROM debian:bullseye
☕ Language Runtime
FROM openjdk:17
FROM python:3.11
FROM node:18
⚡ Minimal Images (Production)
FROM alpine:3.19
✔ Very small size (~5MB) → faster builds & deploys

🚀 Multi-Stage Build (Advanced DevOps Use Case)
You can use multiple FROM statements:
# Stage 1: Build
FROM maven:3.9 AS builder
WORKDIR /app
COPY . .
RUN mvn clean package

# Stage 2: Runtime
FROM openjdk:17
COPY --from=builder /app/target/app.jar /app.jar
CMD ["java", "-jar", "/app.jar"]
🔥 Why this is powerful:
Removes build dependencies from final image
Reduces image size
Improves security.

--------------------------------------------------------------------------------------------------------------------------
🔥 RUN in Dockerfile
--------------------------------------------------------------------------------------------------------------------------
The RUN instruction is used to execute commands during image build time in Docker.
It creates a new image layer after executing the command.
🧱 Basic Syntax
RUN <command>
✅ Example
FROM ubuntu:22.04
RUN apt-get update
RUN apt-get install -y nginx
✔ Each RUN creates a separate layer
⚙️ What Happens Internally?
When Docker hits a RUN instruction:
Creates a temporary container from current image
Executes the command inside it
Commits changes → creates new layer
Removes temporary container

✅ Good (Single layer)
RUN apt-get update && apt-get install -y nginx
✔ Reduces:
Image size
Number of layers
Build time

🧪 Real Examples
📦 Install Packages
RUN apt-get update && apt-get install -y curl vim git
📁 Create Directories
RUN mkdir -p /app/logs
🔧 Build Application
RUN mvn clean package
🧹 Clean Cache (VERY IMPORTANT)
RUN apt-get update && \
    apt-get install -y nginx && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
✔ Prevents unnecessary image bloat

🚀 Shell vs Exec Form
1. Shell Form (Default)
RUN apt-get update && apt-get install -y nginx
✔ Runs via:
/bin/sh -c
2. Exec Form
RUN ["apt-get", "update"]
✔ No shell involved
✔ More secure & predictable
🔥 Advanced: Multi-Line RUN
RUN apt-get update && \
    apt-get install -y nginx curl && \
    mkdir -p /app && \
    useradd appuser
✔ Improves readability + efficiency


⚡ Optimization Tips (Interview Level)
Minimize number of RUN layers
Use multi-stage builds
Use .dockerignore
Prefer lightweight base images (alpine)
Order instructions for caching:
COPY package.json .
RUN npm install
COPY . .
✔ Prevents reinstalling dependencies every build

🎯 Summary
RUN executes commands at build time
Creates new image layers
Used for installing packages, building apps
Should be optimized for size + performance

🧠 DevOps Real-Time Use Case
In CI/CD pipeline:

FROM node:18

WORKDIR /app

COPY package.json .

RUN npm install   # 🔥 installs dependencies at build time

COPY . .

CMD ["npm", "start"]

✔ RUN npm install:
Happens during build
Dependencies baked into image
Faster container startup

--------------------------------------------------------------------------------------------------------------------------
🔥 CMD in Dockerfile
--------------------------------------------------------------------------------------------------------------------------
It is also used to execute the command while creating the container.
We can write multiple CMD instruction. but docker will process only  the last CMD instruction.Hence there is no use of writing multiple CMD instruction inside a dockerfile.

The CMD instruction defines the default command that runs when a container starts in Docker.
Think of it as: “What should this container do when it is launched?”

🧱 Basic Syntax
1. Exec Form (Recommended ✅)
CMD ["executable", "arg1", "arg2"]
2. Shell Form
CMD command arg1 arg2
✅ Example
FROM ubuntu:22.04
CMD ["echo", "Hello World"]

⚙️ What Happens Internally?
When container starts:
Docker checks CMD
Executes it as PID 1 process
If user provides a command → CMD gets overridden

🔥 Real Examples
🌐 Run Web Server
FROM nginx:latest
CMD ["nginx", "-g", "daemon off;"]
✔ Keeps container running in foreground

☕ Java Application
FROM openjdk:17
COPY app.jar /app.jar
CMD ["java", "-jar", "/app.jar"]

🟢 Node.js App
FROM node:18
WORKDIR /app
COPY . .
RUN npm install
CMD ["npm", "start"]


--------------------------------------------------------------------------------------------------------------------------
🔥 COPY in Dockerfile
--------------------------------------------------------------------------------------------------------------------------
It is used to copy the files/dir to a docker images while creating the image .
It is used to copy the file from one location to another/destination location.
The COPY instruction is used to copy files/directories from your local system (build context) into the container image in Docker.
It works only at build time, not at runtime.

🧱 Basic Syntax
COPY <source> <destination>
✅ Example
FROM ubuntu:22.04
COPY test.txt /app/
✔ Copies test.txt → /app/test.txt inside image
📂 What is “Source”?
Source must be inside the build context
Build context = directory where you run:
docker build -t my-image .
🔍 Examples
📄 Copy Single File
COPY app.jar /opt/app/
📁 Copy Directory
COPY myfolder /app/
✔ Copies entire folder recursively
📍 Copy with Rename
COPY app.jar /opt/app/myapp.jar
📦 Copy Multiple Files
COPY file1.txt file2.txt /app/
⚙️ What Happens Internally?
When Docker executes COPY:
Takes files from local system
Transfers them into image filesystem
Creates a new image layer
🚀 Real DevOps Example
FROM openjdk:17
WORKDIR /app

COPY target/app.jar app.jar

CMD ["java", "-jar", "app.jar"]
✔ Workflow:
CI builds .jar
COPY moves it into image
Container runs app
🔥 COPY vs ADD (Important Interview Question)
| Feature             | COPY | ADD              |
| ------------------- | ---- | ---------------- |
| Simple file copy    | ✅    | ✅                |
| Auto extract `.tar` | ❌    | ✅                |
| Download from URL   | ❌    | ✅                |
| Recommended         | ✅    | ❌ (mostly avoid) |

🔥 Best Practice (Layer Caching Optimization)
COPY package.json .
RUN npm install
COPY . .
✔ Why:
Dependencies cached
Faster rebuilds

🧠 Advanced (Ownership)
COPY --chown=appuser:appgroup app.jar /app/
✔ Sets permissions during copy

--------------------------------------------------------------------------------------------------------------------------
🧠 ADD Instruction in Dockerfile
--------------------------------------------------------------------------------------------------------------------------
It is also used to copy the files from source location to destination location while creating the image.
ADD command can download the file from remote location.

🔹 What ADD Does
1. Copy Local Files/Directories
ADD app.py /usr/src/app/
👉 Copies app.py from your local system into the container image.

2. Auto Extract Tar Files (Special Feature)
ADD app.tar.gz /usr/src/app/
👉 If the source is a compressed archive (.tar, .tar.gz, .tgz), Docker will automatically extract it into the destination.

3. Download from URL (Less Common Use)
ADD https://example.com/file.txt /tmp/
👉 Docker downloads the file and places it inside the image.
⚠️ Not recommended in production — use RUN curl or wget for better control.

🔹 Key Difference: ADD vs COPY
| Feature                | ADD ✅ | COPY ✅ |
| ---------------------- | ----- | ------ |
| Copy local files       | ✔️    | ✔️     |
| Auto extract tar files | ✔️    | ❌      |
| Download from URL      | ✔️    | ❌      |
| Simplicity & clarity   | ❌     | ✔️     |

🔹 Best Practice (Very Important)
👉 In real DevOps / production:
Prefer COPY for simple file copying
Use ADD only when you need:
automatic extraction
or URL download (rare)

🔥 Interview-Level Insight
ADD introduces hidden behavior (auto-extract, URL fetch)
This reduces transparency → harder debugging
That’s why most teams enforce:
✅ “Always use COPY unless ADD is strictly required”
🧩 Summary
ADD = powerful but less predictable
COPY = simple, safe, preferred

--------------------------------------------------------------------------------------------------------------------------
🧠 WORKDIR in Dockerfile
--------------------------------------------------------------------------------------------------------------------------

Using this keyword we can set the working directory for the image or container.
It is used to go inside a desired directory.
whatever instruction that we write after WORKDIR all those instructions will be processed in the given working directory only.
The WORKDIR instruction sets the working directory inside the container for all subsequent instructions like RUN, CMD, COPY, ADD, and ENTRYPOINT.
🔹 Syntax
WORKDIR /path/to/directory
🔹 What It Does
Sets the current directory context inside the container
If the directory does not exist → Docker creates it automatically
All following instructions will execute relative to this path

🔹 Example
FROM ubuntu:latest

WORKDIR /app

COPY . .

RUN ls

CMD ["bash"]
👉 What happens here:
WORKDIR /app
Creates /app if not exists
Switches into /app
COPY . .
Copies current local files → /app
RUN ls
Executes inside /app

2. Avoid Errors
Without WORKDIR, commands may run in / (root), causing:
wrong file paths
build failures

🔥 Best Practices (DevOps Perspective)
Always define WORKDIR early in Dockerfile
Use absolute paths for clarity
Avoid frequent directory switching
Combine with COPY for clean structure

--------------------------------------------------------------------------------------------------------------------------
🧠 LABEL in Dockerfile
--------------------------------------------------------------------------------------------------------------------------
It is used to assign the metadata for our image, Label will represent the data in the form of key-value pair.
The LABEL instruction is used to add metadata (key–value pairs) to a Docker image.
This metadata helps in identification, automation, maintenance, and governance of images in real DevOps environments.

🔹 Syntax
LABEL key=value
or multiple labels:
LABEL key1=value1 key2=value2
or multiline (recommended for readability):
LABEL \
  app="myapp" \
  version="1.0" \
  maintainer="akshay"
🔹 What LABEL Does
Attaches metadata to the image
Does NOT affect container runtime behavior
Used for:
Documentation
Image management
Automation pipelines

🔹 Example Dockerfile
FROM ubuntu:latest

LABEL maintainer="akshay@example.com"
LABEL version="1.0"
LABEL description="This is a sample application image"

WORKDIR /app
COPY . .

CMD ["bash"]

🔹 Real DevOps Use Cases
1. Image Versioning
LABEL version="2.3.1"

2. Ownership / Maintainer Info
LABEL maintainer="devops-team@company.com"

3. CI/CD Automation
Tools like Jenkins, Kubernetes, or image scanners use labels to:
identify builds
trigger deployments
track environments

4. Filtering Images
docker images --filter "label=version=1.0"
🔹 Best Practices
Use standardized labels (OCI recommended)
LABEL org.opencontainers.image.title="myapp"
LABEL org.opencontainers.image.version="1.0"
LABEL org.opencontainers.image.authors="akshay"
Keep labels:
meaningful
consistent across images

🔥 Interview Insight
LABEL replaces deprecated MAINTAINER
Multiple LABEL instructions create multiple layers
→ combine them into one for optimization

# ❌ Bad (multiple layers)
LABEL version="1.0"
LABEL author="akshay"

# ✅ Good (single layer)
LABEL version="1.0" author="akshay"
--------------------------------------------------------------------------------------------------------------------------
🧠 EXPOSE in Dockerfile
--------------------------------------------------------------------------------------------------------------------------
It represent on which port number our container should run.
The EXPOSE instruction is used to document which ports a container listens on at runtime.
It does NOT actually publish or open the port — it simply provides metadata for users and tools.

🔹 Syntax
EXPOSE <port>

or multiple ports:
EXPOSE 80 443

or protocol-specific:
EXPOSE 8080/tcp
EXPOSE 53/udp

🔹 What EXPOSE Does
Declares intended network ports
Helps tools (like Docker CLI, orchestration systems) understand container networking
Improves readability of Dockerfile
👉 Think of it as:
“This container expects traffic on these ports"

🔹 Real DevOps Scenario
Application: Node.js App
FROM node:18

WORKDIR /app
COPY . .

RUN npm install

EXPOSE 3000

CMD ["node", "app.js"]
👉 Workflow:
App runs on port 3000 inside container
Dev runs:
docker run -p 3000:3000 myapp
Access via browser:
http://localhost:3000

🔹 Advanced Usage
With Docker Networking (No -p needed)
Containers in same network:
docker network create mynet
docker run -d --network=mynet myapp
👉 Containers communicate internally using exposed ports

🔥 Best Practices
Always define EXPOSE for clarity
Match with actual application port
Don’t rely on it for security
Use -p or orchestration (like Kubernetes) for real exposure

--------------------------------------------------------------------------------------------------------------------------
🧠 VOLUME in Dockerfile
-------------------------------------------------------------------------------------------------------------------------
The VOLUME instruction is used to create a mount point inside the container and mark it as a location for persistent or external storage.
👉 It tells Docker:
“Store this directory’s data outside the container filesystem”

🔹 Syntax
VOLUME ["/path/in/container"]
or
VOLUME /path/in/container
or multiple volumes:
VOLUME ["/data", "/logs"]
🔹 What VOLUME Does
Creates a persistent storage location
Data is stored outside the container layer
Data survives:
container restart ✅
container deletion (if volume is reused) ✅
🔹 Example Dockerfile
FROM mysql:latest

VOLUME /var/lib/mysql

EXPOSE 3306
👉 Here:
/var/lib/mysql → database files
Docker stores DB data in a volume (not inside container)

🔹 How It Works (Behind the Scene)
When container starts:
Docker creates an anonymous volume
Mounts it to /var/lib/mysql
docker run -d mysql
👉 Volume is automatically created and attached

🔹 Types of Volumes
1. Anonymous Volume (via Dockerfile)
VOLUME /data
👉 Docker creates random volume name

2. Named Volume (Recommended in DevOps)
docker run -v mydata:/data myimage
👉 Easier to manage and reuse

3. Bind Mount (Host Mapping)
docker run -v /host/path:/data myimage
👉 Directly maps host directory

🔹 Real DevOps Scenario
Database Persistence (Critical Use Case)

Without volume ❌:
Container deleted → data lost

With volume ✅:
Data persists across deployments

Example:
docker run -d \
  -v mysql_data:/var/lib/mysql \
  -p 3306:3306 \
  mysql

🔥 Best Practices (Very Important)
Avoid relying only on VOLUME in Dockerfile
Prefer named volumes at runtime
Use for:
databases
logs
uploads
Keep application code outside volume

🔹 Example: Node App with Logs Volume
FROM node:18

WORKDIR /app
COPY . .

VOLUME /app/logs

CMD ["node", "app.js"]

🔥 Interview-Level Insight
VOLUME creates a new layer → data written after that is NOT part of image
You cannot modify volume data during build after declaring it
That’s why:
❗ Place VOLUME instruction carefully

--------------------------------------------------------------------------------------------------------------------------
🧠 MAINTAINER in Dockerfile
--------------------------------------------------------------------------------------------------------------------------
The MAINTAINER instruction was used to specify the author/owner of a Docker image.
🔴 Current Status (Very Important)
👉 MAINTAINER is DEPRECATED (no longer recommended)
Deprecated since Docker 1.13
Replaced by LABEL instruction

--------------------------------------------------------------------------------------------------------------------------
🧠 USER Instruction in Dockerfile
--------------------------------------------------------------------------------------------------------------------------

The USER instruction in a Dockerfile is used to define which user (UID/GID) will execute subsequent instructions (RUN, CMD, ENTRYPOINT, etc.) inside the container.

🔹 Syntax
USER <username>
or
USER <UID>:<GID>

🔹 Why USER is Important?
By default, Docker containers run as root user, which is:
❌ Security risk (privilege escalation possible)
❌ Not recommended for production
Using USER:
✅ Improves container security
✅ Follows least privilege principle
✅ Prevents accidental system-level changes

🔹 Example 1: Basic Usage
FROM ubuntu:latest

# Create a new user
RUN useradd -m appuser

# Switch to non-root user
USER appuser

CMD ["whoami"]

👉 Output when container runs:
appuser
🔹 Example 2: Using UID and GID
FROM alpine

RUN addgroup -g 1001 appgroup && \
    adduser -D -u 1001 -G appgroup appuser

USER 1001:1001

CMD ["id"]

🔹 Example 3: Switching Between Users
FROM ubuntu

RUN useradd -m appuser

# Run as root
RUN apt-get update

# Switch to appuser
USER appuser

# This runs as appuser
RUN whoami

🔹 Real DevOps Use Case
In production (e.g., Node.js / Java apps):
FROM node:18

WORKDIR /app

COPY . .

RUN npm install

# Create non-root user
RUN useradd -m nodeuser

# Change ownership
RUN chown -R nodeuser:nodeuser /app

USER nodeuser

CMD ["node", "app.js"]
👉 Why this matters:
Prevents container from running as root
Reduces attack surface in Kubernetes / ECS

🔹 Important Notes
| Point                     | Explanation                             |
| ------------------------- | --------------------------------------- |
| Default user              | root                                    |
| Scope                     | Affects all instructions after it       |
| Can switch multiple times | Yes                                     |
| Best practice             | Always use non-root user                |
| File permissions          | Must ensure correct ownership (`chown`) |


🔥 Interview Answer (Short)
USER instruction defines which user will run the container processes. By default, Docker uses root, but for security best practices, we switch to a non-root user to reduce privilege risks and follow least privilege principles.

--------------------------------------------------------------------------------------------------------------------------
🧠 ENTRYPOINT Instruction in Dockerfile
--------------------------------------------------------------------------------------------------------------------------
ENTRYPOINT defines the main executable (fixed command) that will always run when a container starts.
Think of it as:
👉 “This container is designed to run THIS application only.”
ENTRYPOINT instruction will be executed while creating the container.
ENTRYPOINT instruction cannot be overwritten. CMD instruction can be overwritten.

🔹 Key Concept
| Feature           | ENTRYPOINT                           |
| ----------------- | ------------------------------------ |
| Purpose           | Defines main process                 |
| Override behavior | Hard to override                     |
| Works with CMD    | Yes (CMD provides default arguments) |

🔹 ENTRYPOINT vs CMD
| Feature     | ENTRYPOINT    | CMD               |
| ----------- | ------------- | ----------------- |
| Purpose     | Fixed command | Default arguments |
| Overridable | ❌ Hard        | ✅ Easy            |
| Use case    | Main app      | Parameters        |


How Docker combines ENTRYPOINT and CMD internally.
Let’s break it down precisely.
🧠 Core Rule
When both are defined:
ENTRYPOINT ["python"]
CMD ["app.py"]
👉 Docker combines them as:
python app.py
🔹 How It Works Internally
Docker treats it as:
ENTRYPOINT + CMD = FINAL COMMAND
So:
ENTRYPOINT → fixed executable
CMD → default arguments

--------------------------------------------------------------------------------------------------------------------------
🧠 ENV Instruction in Dockerfile
--------------------------------------------------------------------------------------------------------------------------
ENV is used to set environment variables inside a container, which are available:
During build time (for subsequent instructions like RUN)
During runtime (inside the running container)
🔹 Syntax
1. Key-Value Format (Recommended ✅)
ENV KEY=value
2. Multiple Variables
ENV KEY1=value1 KEY2=value2
🔹 Basic Example
FROM ubuntu

ENV APP_ENV=production

CMD ["printenv", "APP_ENV"]
👉 Output:
production

🔹 How It Works
ENV variables persist throughout the image
Accessible in:
RUN commands
Application runtime
Shell inside container

🔹 Example with RUN
FROM ubuntu

ENV NAME=Akshay

RUN echo "Hello $NAME"
👉 During build:
Hello Akshay

🔹 Real DevOps Use Case
Node.js App Configuration
FROM node:18

WORKDIR /app
COPY . .

ENV NODE_ENV=production
ENV PORT=3000

RUN npm install

CMD ["node", "app.js"]
👉 Inside container:
echo $NODE_ENV   → production
echo $PORT       → 3000

🔥 Interview Answer (Short)
ENV is used to define environment variables in a Docker image that are available both during build time and container runtime. These variables help configure applications dynamically without hardcoding values.

🚀 Pro Tips (DevOps)
Use ENV for:
App configs (PORT, NODE_ENV)
Feature flags
Avoid storing:
❌ Secrets (use Docker secrets / AWS SSM / Vault)
Combine multiple ENV in one layer (optimization)

--------------------------------------------------------------------------------------------------------------------------
🧠 ARG Instruction in Dockerfile
--------------------------------------------------------------------------------------------------------------------------
🔥 Interview Answer (Short)
ARG is used to pass build-time variables to a Dockerfile. These variables are only available during the image build process and are not accessible in the running container, unlike ENV.

🔥 Interview Answer (Short)
ARG is used to pass build-time variables to a Dockerfile. These variables are only available during the image build process and are not accessible in the running container, unlike ENV.

--------------------------------------------------------------------------------------------------------------------------
🔥 USER in Dickerfile
--------------------------------------------------------------------------------------------------------------------------
We can set the username for an image or a container , By default , most docker images run the processe as "root" user only.

USER helps you switch from root user to custom user.


🔥CMD ["bash" , "-c" , "echo 'hello > /data/helllo.txt"]-----> The CMD instruction will process the required task and once the task is completed sucessfully ,CMD instruction will make the container to go into EXITED State.🔥
--------------------------------------------------------------------------------------------------------------------------
🔥FOREGROUND🔥
--------------------------------------------------------------------------------------------------------------------------

🧠 Correct Understanding
Foreground vs Background is NOT just about interaction — it’s about how the container process is attached to your terminal.

🔹 More Accurate Definition
Concept	Meaning
Foreground	Container is attached to your terminal (interactive)
Background	Container is detached and managed by Docker daemon

🔍 Where You Are Right ✅
Yes — it affects how you interact:
Foreground → direct interaction (keyboard + logs)
Background → indirect interaction (logs, exec, APIs)
❗ But Here’s the Important Correction
⚠️ It’s NOT just interaction — it’s about process attachment + I/O streams

🔥 Deep Technical View

Foreground Mode
Your Terminal ↔ STDIN/STDOUT ↔ Container Process (PID 1)
Direct stream connection
Signals passed instantly (Ctrl+C)
Interactive

Background Mode
Docker Daemon → Container Process
        ↑
   (logs stored internally)
No terminal attachment
Runs independently
You interact via Docker CLI APIs

🔹 Practical Comparison

Foreground
docker run -it ubuntu /bin/bash
👉 You:
Type commands
See output instantly

Background
docker run -d nginx
👉 You:
Don’t see logs directly
Use:
docker logs
docker exec -it

🔥 Critical Insight (Very Important)

🚨 Interaction is a side effect — not the core concept
The core concept is:
ATTACHED vs DETACHED execution

🔹 Better Mental Model
Instead of thinking:
Foreground = interaction
Think:
Foreground = attached process
Background = detached process

🔹 Real DevOps Perspective
Developers → use foreground (debugging)
Production systems → use background
Kubernetes → always detached (no terminal)

🔥 Interview-Level Answer
Foreground and background in Docker refer to whether the container process is attached or detached from the terminal. While it affects how we interact with the container, the core concept is process attachment and how input/output streams are handled.

========================================================================================================================
 🐳  Docker project-1 (portfolio website deployment) 🐳 
========================================================================================================================
# Clone the repo
git clone https://github.com/yourusername/portfolio.git
cd portfolio

🌍 Deploy to GitHub Pages (Free Hosting)

# Step 1: Initialize git (if not done)
git init
git add .
git commit -m "Initial portfolio commit"

# Step 2: Create repo on GitHub (github.com → New Repository)
# Name it: portfolio  (or yourusername.github.io for root domain)

# Step 3: Push to GitHub
git remote add origin https://github.com/yourusername/portfolio.git
git branch -M main
git push -u origin main

# Step 4: Enable GitHub Pages
# Go to: Settings → Pages → Source: Deploy from branch → main → / (root) → Save

# Your site will be live at:
# https://yourusername.github.io/portfolio
🐳 Run in Docker (for hosting inside a container)

# Build the Docker image
docker build -t akshay-portfolio .

# Run the container
docker run -d -p 80:80 --name portfolio akshay-portfolio

# Access at: http://localhost
Dockerfile (create this file):

FROM nginx:alpine
COPY . /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]




--------------------------------------------------------------------------------------------------------------------------
🐳 Docker Volumes — Deep Dive + Hands-On Demo  --------------------------------------------------------------------------------------------------------------------------
Volume indicates 'storage'
It is a directory inside a container
We will use volumes to store the data of containers.
Conatiner------> created a files----> Delete the container . The data stored in the container will also get deleted .
To persist the data we will use docker volumes concept.

We can attach a single volume to multiple containers.

Docker volumes are persistent storage mechanisms used to retain data outside the container lifecycle.

🔍 1. Why Volumes Are Needed
Problem:
Containers are ephemeral
When container stops/deletes → data is lost
Solution:
Volumes store data outside container filesystem

🧠 2. Types of Storage in Docker
| Type           | Persistence  | Use Case                 |
| -------------- | ------------ | ------------------------ |
| **Volume**     | ✅ Persistent | Production data          |
| **Bind Mount** | ✅ Persistent | Dev/testing (local path) |
| **tmpfs**      | ❌ Temporary  | Sensitive/temp data      |


------------------------------------------------------------------
🐳 Creating a volume using Dockerfile
------------------------------------------------------------------
1-create a dockerfile vim docker and write a instruction
FROM ubuntu
VOLUME ["volume1"]
2- docker build -t volume:v1 .
3- docker images
4- docker run -it --name cont-1 volume:v1 /bin/bash
5- now go inside the container in folder /volume1 and create a sample 10 file.txt
6- All data that is stored in volume1 is stored on Host machine (EC2 instance ) that means if container delete or restart your data remain safe / persistent you can retrive data form Host .
7- Docker has its own HOME DIRECTORY ---->var/lib/docker
8-Now exit from the container with -->Ctl + P + Q
9- Now go the docker home directory /var/lib/docker. You will get directory volumes and inside data is available . There is an Volume ID go inside  --->cd _data ---> All files are available check and confirm.
=========================================================================================================================
root@ip-172-31-30-168:/# cd /var/lib/docker/

root@ip-172-31-30-168:/var/lib/docker# ls
buildkit  containers  engine-id  image  network  overlay2  plugins  runtimes  swarm  tmp  volumes

root@ip-172-31-30-168:/var/lib/docker# cd volumes/

root@ip-172-31-30-168:/var/lib/docker/volumes# ls
4eee37713e82cf88e896e2eb75383f5d47932caa1d7d1b9316567af81a6d78a3  backingFsBlockDev  metadata.db

root@ip-172-31-30-168:/var/lib/docker/volumes# cd 4eee37713e82cf88e896e2eb75383f5d47932caa1d7d1b9316567af81a6d78a3/

root@ip-172-31-30-168:/var/lib/docker/volumes/4eee37713e82cf88e896e2eb75383f5d47932caa1d7d1b9316567af81a6d78a3# ls
_data

root@ip-172-31-30-168:/var/lib/docker/volumes/4eee37713e82cf88e896e2eb75383f5d47932caa1d7d1b9316567af81a6d78a3# cd _data/

root@ip-172-31-30-168:/var/lib/docker/volumes/4eee37713e82cf88e896e2eb75383f5d47932caa1d7d1b9316567af81a6d78a3/_data# ls
file1.txt   file2.txt  file4.txt  file6.txt  file8.txt
file10.txt  file3.txt  file5.txt  file7.txt  file9.txt
=========================================================================================================================
10- So whatever data u have created inside container is available on EC2 instance that is what docker Do.
11-Now create a additional file form host machne inside the _data folder on Ec2 instance .

1a6d78a3/_data# touch hello{1..5}.java
root@ip-172-31-30-168:/var/lib/docker/volumes/4eee37713e82cf88e896e2eb75383f5d47932caa1d7d1b9316567af81a6d78a3/_data# ls
file1.txt   file2.txt  file4.txt  file6.txt  file8.txt  hello1.java  hello3.java  hello5.java
file10.txt  file3.txt  file5.txt  file7.txt  file9.txt  hello2.java  hello4.java
root@ip-172-31-30-168:/var/lib/docker/volumes/4eee37713e82cf88e896e2eb75383f5d47932caa1d7d1b9316567af81a6d78a3/_data#

12- So if i create any data in this path ----> /var/lib/docker/volumes/4eee37713e82cf88e896e2eb75383f5d47932caa1d7d1b9316567af81a6d78a3/_data 
all this data is Replicated inside the container where the volume we have created.And Vice-Versa also. So it is Bidirectional.
13- Now go inside the container ------> docker exec -it <cont-ID> /bim/bash

root@ip-172-31-30-168:~# docker exec -it 91303710f68c /bin/bash
root@91303710f68c:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  volume1

root@91303710f68c:/# cd volume1/
root@91303710f68c:/volume1# ls
file1.txt   file2.txt  file4.txt  file6.txt  file8.txt  hello1.java  hello3.java  hello5.java
file10.txt  file3.txt  file5.txt  file7.txt  file9.txt  hello2.java  hello4.java

root@ip-172-31-30-168:~# docker volume ls
DRIVER    VOLUME NAME
local     4eee37713e82cf88e896e2eb75383f5d47932caa1d7d1b9316567af81a6d78a3

14 - Now to attach this volume1 that we have previously created to multiple containers .so all files we created in volume1 is available in cont-2 also. check it so volume1 as shared between cont-1 and cont-2. Now if you write a data in volume1 irrespective with any container the data is replicated in both coantainers.Because Both containers is attached with same volume1.


root@ip-172-31-30-168:~# docker run -it --name cont-2 --volumes-from cont-1 ubuntu
\\root@bd828144b808:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  volume1
root@bd828144b808:/# cd volume1/
root@bd828144b808:/volume1# ls
file1.txt   file2.txt  file4.txt  file6.txt  file8.txt  hello1.java  hello3.java  hello5.java
file10.txt  file3.txt  file5.txt  file7.txt  file9.txt  hello2.java  hello4.java
 
15 - Now create a new cont-3 with volume which is available in cont-2 command is ---> docker run -it --name cont-3 --volumes-from cont-2 ubuntu
 
==========================================================================================================================================
🐳Method-2 : Create a volume using Command 🐳
==========================================================================================================================================
1- Now create a new cont-4 with new volume  ----> docker run -it --name cont-4 -v /volume2 ubuntu
2-  Now create cont-5 using the volume which there in contaner-4 ----> docker run -it --name cont-5 --volumes-from cont-4 ubuntu
3- you wil get the  same data in cont-5  which  is available in cont-4. cd /volume2 
4- Now if we deleted cont-4  and cont-5 the volume2 is available on host it will not get deleted --->  docker volume ls , check the volume is available.
5- Now go cd /var/lib/docker/volumes/4eee37713e82cf88e896e2eb75383f5d47932caa1d7d1b9316567af81a6d78a3 ----> ls _data , data is persisted.
6-  NOTE-  Data is persisted on EC2 instance but What if some one deleted instance or instance  crash ? What happen to this volume ?--> Everythings get deleted. so it is not recommended in realtime usecase, In realtime we use EBS volume to attach container.

===================================================================================================================================================
🐳 Method-3 : Attaching EBS volume to Container 🐳
===================================================================================================================================================
1- Now we will attach EBS volume to container , even if some one deleted EC2 instancemy data is safe and secure in EBS volume , 
2- You can change the EBS volume setting --->Delete on Termination - NO , By Default it is YES, we need to modify or need to attach new EBS volume to EC2.
3- Now are will create a New EBS volume and for that EBS volume i will  attach the container. 
4- Create new EBS Volume----> Attach to EC2 Instance -----> Containers are available -----> Attach EBS volume to the container.
NOTE: We need to create EBS volume in the same AZ as of EC2 instance.
5- Now create a volume ----> attach the volume to Ec2 instance ---> select device name /dev/sdb--> Attach volume ---->It should be show status --> In use
6- Now whatever data we write can store in /dev/sdb volume.
7- Now let us attach this volume to container for this we need to work with mounting a filesystem.
8- lsblk -----> First time when you create volume it is recommended to format a volume-----> sudo mkfs.ext4 /dev/xvdb 
9- Now  to mount a EBS volume permenently we nedd to create a folder(Dir) ----> sudo  mkdir -p /mnt/ebs----> df -h /mnt/ebs 
10- We need to give permission to user to write data in /mnt/ebs ----> sudo chown -R ec2-user:ec2-user /mnt/ebs   
11- Set permissions so Docker can read/write  ---> chmod -R 755 /mnt/ebs .
12- To make data persisted we need to add Block UUID ---->sudo blkid /dev/ebs ---->copy UUID ----->run the command ---->sudo bash -c 'echo "UUID=1234-ABCD /mnt/ebs ext4 defaults,nofail 0 2" >> /etc/fstab'------->“This command appends a persistent mount entry into /etc/fstab using the volume UUID, ensuring the EBS volume is automatically mounted on reboot. nofail prevents boot failure if the volume is unavailable, and sudo bash -c ensures proper permission for file modification.”
13- After adding the configuration in /etc/fstab it is recommanded to unmount and mount the Directory ----> sudo unmount /mnt/ebs ----> sudo mount -a 
14- Check ----> df -h /mnt/ebs 
15- All these are mandatory steps to create a  EBS volume data persistent .

16- Create new application and add data in /mnt/ebs and check the data is persisted ro not?
17-  mkdir ebs-flask ----->vim app.py (copy the code from kastro kiran notes Docker_Day 05 and paste in app.py file) 
18- Install dependencies ----> vim requirements.txt (copy the code from kastro kiran notes Docker_Day 05 and paste in requirement.txt file)
19- Now write a dockerfile for python application.
FROM python:3.11
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirement.txt
COPY app.py .
EXPOSE 5000
CMD ["python","app.py"]

20- Now build the Docker image -----> docker build -t ebs-flask:v1 .
21- Now run the container with the Image and mount the volume path to persiste data -----> docker run -it --name ebs-cont-1 -p 5001:5000 -v /mnt/ebs:/data ubuntu 
22- Now the open the Host port number in the security group of EC2 instance and copy public IP and paste in the browser-----> IP:<Hostport> 
23- Open the application and add some data . Now check that data is stored in /mnt/ebs ---->ls -l /mnt/ebs ---->To check the data---->sudo apt install sqlite -y------> sqlite3 /mnt/ebs/app.db "SELECT * FROM entries;"
24- Now stop container and check the data persisted.
25- Now again create a new conatiner  ---> docker run -it --name ebs-cont-1 -p 5001:5000 -v /mnt/ebs:/data ubuntu -->Now old data will get back to the conatiner.
26- Chek the data --->sqlite3 /mnt/ebs/app.db "SELECT * FROM entries;"
27- To check the which volume is attach to which container -----> docker inspect <containerID> | grep -i volume 
 
                                                       ✅ DONE ----> Data is persisted . ✅ 

==========================================================================================================================
🐳 Docker Images — Deep Dive (DevOps-Oriented)  0️⃣  1️⃣ 2️⃣ 3️⃣ 4️⃣ 5️⃣ 6️⃣ 7️⃣ 8️⃣ 9️⃣ 🔟 
==========================================================================================================================
🔹 What is a Docker Image?
A Docker Image is an immutable, read-only template used to create containers. It includes:
Application code
Runtime (e.g., Java, Python, Node)
Libraries & dependencies
OS layer (minimal Linux distribution)
👉 Think of it as a snapshot of a filesystem + execution config.

🔹 Internal Architecture of Docker Image
📦 Layered Structure
Base Image (Ubuntu/Alpine)
   ↓
Dependencies Layer
   ↓
Application Code Layer
   ↓
Configuration Layer

-Each instruction in a Dockerfile creates a new layer
-Layers are cached → improves build performance
-Uses Union File System (OverlayFS)

-----------------------------------------------------
🔹 Types of Docker Images (with Real Use Cases)
-----------------------------------------------------

1️⃣ Base Images (Minimal OS Images)
These are the foundation images without any parent image.
These are used as starting points for building custom images.
These are usually provided by OS vendors or DockerHub.
A base image is the foundation layer of a Docker container image. It's an image that has no parent image (usually specified with FROM scratch in Dockerfile) or is the starting point for building your custom images. It typically contains an operating system, runtime environment, and essential dependencies.

---------------------------------------------
🐳 Types of Base Images in Docker
---------------------------------------------
when you build a docker image, the base image decide 4 important factors,
1- How Big the image will be?
2- What tools and packages are inside?
3- How Secure it is ?
4- And how fast it runs?

A base image is the starting layer (FROM) of your Docker image. It defines:
-OS / runtime environment
-Available packages & tools
-Security posture
-Final image size


1- Standard Images 
- Size: Large(300MB-1GB)
- content: Full OS with package manager, build Tools, documentations...
- Usecases: Development stage 
- Ex: python:3.9, node:16, ubuntu:24.

2- Slim Image 
- Size:Medium ( 50MB-150MB+)
- content: Minimal OS with basic utilities , no build tools.
- Usecase: Production ,Smaller than standard images still familiar.
- Python:3.9-slim, node:16-slim, ubuntu:24-slim...

3- Alpine Images 
- which are having smallest size: (2MB-50MB+)
- Content: apk-package managers,musl libc ,Alpine Linux.
- Usecase: python:3.9-alpine, node:16-alpine, ubuntu:24-alpine...

4- Distroless Images (Officially created by Google )
- size:Bery small(2MB- 30MB+)(It only contain application runtime , no shell, no package manager) , Harder to Debug(cann exec into the container to run commands, because it doesnt have shell)
-  Usecase: Immutable containers.
- python:gcr.io/distroless/python3,gcr.io/distroless/nodejs.. (Google container registry)

5- Scratch Images
- size: 0 MB
- contents: Absolutely empty,No shell,no OS , nothing.

Choosing the right type of base image;
The right type of base image depends on the type of the programming language used to develop the application.

Important Note:

🐹 - For Go Application ( You can go with , these images ---->Distroless, alpine,scratch)

🐍 - For python Applications (You can go with , these images ----> slim),
NOTE:  Always Avoid Alpine image for python application unless you know how to deal with musl issue .

🟢 - For Node.js Appliaction  ( node:18-slim)

♨️ - For Java Applications ( use eclipse-temurin:21-jdk, openJDK:21-slim)  

--------------------------------------------------------------------------------------------------------------------------

2️⃣ Child Images 
These images are built on top of base images.
Add specific application layers, dependencies or configurations
ex: FROM python:3.11-slim 
-------------------------------------------------------------------------------------------------------------------------
3️⃣ Official Language Runtime Images
Images maintained by language community(python,java,Golang,Node).
ex: FROM python:3.9 (900MB)
    FROM python:3.9-slim (150MB)
    FROM python:3.9-alpine (50MB)
🔹 Key Characteristics
✅ 1. Verified & Secure
Minimal vulnerabilities (frequent CVE patches)
Official maintainers handle updates
✅ 2. Standardized Structure
Consistent environment variables
Predictable behavior across environments
✅ 3. Multiple Variants Available
Example (node image):
node:18
node:18-alpine
node:18-slim
| Variant | Use Case               |
| ------- | ---------------------- |
| Full    | Dev / testing          |
| Alpine  | Lightweight production |
| Slim    | Balanced               |

🔹 Real DevOps Use Cases
🔸 Use Case 1: Microservices Architecture
API service → node image
Database → postgres image
Cache → redis image
Reverse proxy → nginx
👉 All official images → fast setup + reliability

🔹 Key Takeaways
Official images = trusted building blocks
Use them for:
Infrastructure (DB, cache, proxy)
Base images for apps
Combine with:
Custom builds
Multi-stage
Security hardening
--------------------------------------------------------------------------------------------------------------------------
4️⃣ Application-Specific Docker Images
🔹 What are Application-Specific Images?
Application-specific images are custom-built Docker images that package:
Your application code
Required runtime (Java, Node, Python, etc.)
Dependencies & libraries
Configuration
👉 In short:
“Your complete application bundled into a portable, runnable unit.”
🔹 How They Differ from Official Images
| Aspect        | Official Image   | Application-Specific Image      |
| ------------- | ---------------- | ------------------------------- |
| Purpose       | Generic service  | Your app                        |
| Example       | `nginx`, `mysql` | `my-company-payment-service:v1` |
| Ownership     | Docker/Community | You / Organization              |
| Customization | Limited          | Full control                    |

Base Image (node:18-alpine)
   ↓
Install Dependencies (npm install)
   ↓
Copy Application Code
   ↓
App Configuration
   ↓
Start Command (CMD)

🔹 When to Use Application-Specific Images
| Scenario              | Why                          |
| --------------------- | ---------------------------- |
| Production deployment | Immutable & reproducible     |
| CI/CD pipelines       | Consistent build environment |
| Microservices         | Isolation per service        |
| Scaling apps          | Easy replication             |


Base Image----> Starts from Scratch
Child Image----> Extended base with language run time (Pre-installed language like python, node, java)
Official Images----> which are generated by language community these are verifed by the officials Documents.(No Bugs, Vurnuablaties) , Production Grade images
Application Images--->Which are pre-build images for specific applications deployment (nginx)

==========================================================================================================================
🐳 Docker Project-2  🐳
==========================================================================================================================
🐳 Docker Image Types — Size Comparison & Best Practices

Docker Python Linux AWS

A hands-on demo comparing Docker base image types — sizes, use cases, and when to choose what.

Built by Akshay Sawant — AWS DevOps Engineer | Pune

📌 Table of Contents

Project Overview
Real Output — My Docker Experiment
The 5 Types of Docker Images
Image Size Comparison Table
Which Image for Which Language?
Dockerfile Examples
Key Learnings
Folder Structure
How to Run This Project
🎯 Project Overview

"The Docker image you choose decides 4 critical things: Size, Security, Speed, and Tooling."
When building containerized applications, the base image you pick has a huge impact on:

Factor	Impact
📦 Image Size	Affects pull time, storage, and bandwidth cost
🔒 Security	More packages = more CVE vulnerabilities
⚡ Startup Speed	Smaller images boot faster
🛠️ Debugging	Some images have no shell at all
This project runs the same Python Flask app on 4 different base images and compares the results — with real terminal output from my local Docker setup.

📊 Real Output — My Docker Experiment

Here's the actual docker images output from my MacBook running Docker Desktop:

IMAGE                     ID             DISK USAGE   CONTENT SIZE
─────────────────────────────────────────────────────────────────────
py-standard:latest        dedce3dfe223     1.62 GB        404 MB    ← Full Python 3.11
py.slim:latest            7cecabaa3473      236 MB        51.4 MB   ← Python 3.11-slim
py.alpine:latest          a99dc25f453d     88.5 MB        20.9 MB   ← Python 3.11-alpine
py.distroless:latest      f62a69de4113      223 MB        51.7 MB   ← Google Distroless
🔥 Size Reduction Achieved:

py-standard   ████████████████████████████████████████  1.62 GB  (baseline)
py.slim       ██████                                     236 MB   ↓ 85% smaller
py.distroless ██████                                     223 MB   ↓ 86% smaller
py.alpine     ██                                         88.5 MB  ↓ 94% smaller
94% size reduction by switching from python:3.11 → python:3.11-alpine for the same app!
🐳 The 5 Types of Docker Images

1️⃣ Standard / Full Images

"The kitchen sink — everything included"
FROM python:3.11
Size: 300 MB – 1.6 GB
Contains: Full OS, package manager (apt), build tools, documentation, dev utilities
Best For: Local development, debugging, CI build stages
Example Tags: python:3.11, node:18, ubuntu:24.04
✅ Easy to debug          ✅ All tools available
✅ No dependency issues   ❌ Very large size
❌ More attack surface    ❌ Slow to pull/push
2️⃣ Slim Images

"Production-ready, batteries not included"
FROM python:3.11-slim
Size: 50 MB – 150 MB
Contains: Minimal Debian/Ubuntu OS, basic utilities, no build tools
Best For: Production deployments, balanced size vs. compatibility
Example Tags: python:3.11-slim, node:18-slim
✅ Much smaller than full   ✅ Familiar Debian base
✅ Good for production      ❌ Need to install build deps manually
✅ Easy migration from full ❌ Slightly larger than Alpine
3️⃣ Alpine Images

"Ultralight — but know what you're doing"
FROM python:3.11-alpine
Size: 2 MB – 50 MB
Contains: Alpine Linux, apk package manager, musl libc (NOT glibc!)
Best For: Go apps, simple scripts, microservices with no C-extensions
Example Tags: python:3.11-alpine, node:18-alpine, nginx:alpine
✅ Smallest possible size   ✅ Fast to pull and deploy
✅ Great for Go/Node        ❌ musl libc ≠ glibc (breaks Python C-extensions)
⚠️  Avoid for Python unless you handle musl carefully
⚠️ Important Warning for Python developers: Alpine uses musl libc instead of glibc. Many Python packages like numpy, pandas, scipy have C extensions compiled against glibc — they will fail to install or run on Alpine. Use slim instead for Python.
4️⃣ Distroless Images (by Google)

"No shell, no OS tools, just your app"
FROM gcr.io/distroless/python3
Size: 2 MB – 30 MB (runtime only)
Contains: Only the language runtime. No shell, no package manager, no OS utilities
Best For: Immutable production containers with maximum security
Registry: gcr.io/distroless
✅ Maximum security (no shell = no shell injection)
✅ Smallest attack surface
✅ Used in security-critical production systems
❌ Cannot exec into container for debugging (no /bin/sh)
❌ Complex multi-stage build required
❌ Harder to troubleshoot issues
Typical multi-stage pattern with Distroless:

# Stage 1: Build
FROM python:3.11 AS builder
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY app.py .

# Stage 2: Run (Distroless — no shell!)
FROM gcr.io/distroless/python3
WORKDIR /app
COPY --from=builder /usr/local /usr/local
COPY --from=builder /app /app
CMD ["app.py"]
5️⃣ Scratch Images

"Absolute zero — start from nothing"
FROM scratch
Size: 0 MB (literally empty)
Contains: Absolutely nothing — no OS, no shell, no tools
Best For: Statically compiled binaries only (Go, Rust, C)
✅ Absolute minimum size     ✅ Maximum security
✅ Perfect for Go binaries   ❌ Requires statically compiled app
❌ Impossible to debug       ❌ Not suitable for Python/Node/Java
📐 Image Size Comparison Table

Image Type	Base Tag Example	Typical Size	Shell	Package Mgr	Use Case
🔵 Standard	python:3.11	300MB–1.6GB	✅ bash	✅ apt	Development
🟡 Slim	python:3.11-slim	50MB–150MB	✅ sh	✅ apt (minimal)	Production
🟢 Alpine	python:3.11-alpine	5MB–50MB	✅ sh	✅ apk	Lightweight prod
🔴 Distroless	gcr.io/distroless/python3	2MB–30MB	❌ None	❌ None	Secure production
⬛ Scratch	scratch	0 MB	❌ None	❌ None	Go/Rust binaries
🎯 Which Image for Which Language?

🐹  Go Application
    ┌─────────────────────────────────────────────────────┐
    │  BEST CHOICES:  scratch > distroless > alpine       │
    │  REASON: Go compiles to a static binary             │
    │  AVOID:  Standard (overkill)                        │
    └─────────────────────────────────────────────────────┘

🐍  Python Application
    ┌─────────────────────────────────────────────────────┐
    │  BEST CHOICES:  slim > distroless                   │
    │  REASON: C-extensions need glibc (Debian-based)     │
    │  AVOID:  Alpine ⚠️  (musl libc breaks many packages) │
    └─────────────────────────────────────────────────────┘

🟢  Node.js Application
    ┌─────────────────────────────────────────────────────┐
    │  BEST CHOICES:  node:18-slim > node:18-alpine       │
    │  REASON: Slim for npm packages needing build tools  │
    │  AVOID:  Standard in production                     │
    └─────────────────────────────────────────────────────┘

♨️  Java Application
    ┌─────────────────────────────────────────────────────┐
    │  BEST CHOICES:  eclipse-temurin:21-jdk-slim         │
    │                 gcr.io/distroless/java21             │
    │  REASON: JVM needs proper glibc support             │
    │  AVOID:  Alpine (JVM + musl = compatibility issues) │
    └─────────────────────────────────────────────────────┘

🦀  Rust Application
    ┌─────────────────────────────────────────────────────┐
    │  BEST CHOICES:  scratch > distroless > alpine       │
    │  REASON: Rust compiles to static binaries           │
    └─────────────────────────────────────────────────────┘
📄 Dockerfile Examples

All 4 Dockerfiles used in this project for the same Python Flask app:

dockerfile — Standard

FROM python:3.11
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY app.py .
EXPOSE 8080
CMD ["python", "app.py"]
📦 Result: 1.62 GB — Full Debian + all Python tools
dockerfile.slim — Slim

FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY app.py .
EXPOSE 8080
CMD ["python", "app.py"]
📦 Result: 236 MB — Minimal Debian, pip still works perfectly
dockerfile.alpine — Alpine

FROM python:3.11-alpine AS builder
WORKDIR /app
COPY app.py .

FROM python:3.11-alpine
WORKDIR /app
COPY --from=builder /app/app.py .
CMD ["python", "app.py"]
📦 Result: 88.5 MB — Smallest, but watch for musl issues with complex deps
dockerfile.distro — Distroless (Multi-stage)

FROM python:3.11 AS base
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY app.py .

FROM gcr.io/distroless/python3:latest
WORKDIR /app
COPY --from=base /usr/local /usr/local
COPY --from=base /app /app
CMD ["app.py"]
📦 Result: 223 MB — No shell, maximum security for production
💡 Key Learnings

✅ What I Learned from This Experiment

1. Image naming matters!
   ❌ Wrong:  docker run py-slim       (Docker tries to pull from registry)
   ✅ Correct: docker run py.slim      (uses local image with dot notation)

2. pip-install ≠ pip install
   ❌ Wrong:  RUN pip-install --no-cache-dir -r requirements.txt
   ✅ Correct: RUN pip install --no-cache-dir -r requirements.txt

3. Alpine alpine app.py crash (Exit code 1)
   Reason: Missing dependencies or musl incompatibility
   Solution: Add build deps or switch to slim

4. docker build typo
   ❌ Wrong:  docker built -t ...
   ✅ Correct: docker build -t ...
🏆 The Decision Framework

Are you in DEVELOPMENT?
  └── YES → Use Standard (python:3.11) — full tooling, easy debug

Are you going to PRODUCTION?
  ├── Python app?
  │     └── Use SLIM (python:3.11-slim) — best compatibility + small size
  ├── Go / Rust app?
  │     └── Use SCRATCH or DISTROLESS — static binary, zero overhead
  ├── Node.js app?
  │     └── Use SLIM or ALPINE — check npm deps for native modules
  └── Security-critical system?
        └── Use DISTROLESS — no shell = reduced attack surface
📁 Folder Structure

docker-image-types/
├── app1/
│   ├── app.py                  # Simple Python Flask app
│   ├── requirements.txt        # Flask dependency
│   ├── dockerfile              # Standard: python:3.11
│   ├── dockerfile.slim         # Slim: python:3.11-slim
│   ├── dockerfile.alpine       # Alpine: python:3.11-alpine
│   └── dockerfile.distro       # Distroless: gcr.io/distroless/python3
└── README.md                   # This file
🚀 How to Run This Project

# Clone the repository
git clone https://github.com/yourusername/docker-image-types.git
cd docker-image-types/app1

# Build all 4 images
docker build -t py-standard    -f dockerfile       .
docker build -t py.slim        -f dockerfile.slim   .
docker build -t py.alpine      -f dockerfile.alpine .
docker build -t py.distroless  -f dockerfile.distro .

# Check sizes
docker images | grep "py"

# Run them side by side on different ports
docker run -d --name python.standard  -p 9001:8080 py-standard
docker run -d --name python.slim      -p 9002:8080 py.slim
docker run -d --name python.alpine    -p 9003:8080 py.alpine

# Test each
curl http://localhost:9001
curl http://localhost:9002
curl http://localhost:9003

# Clean up
docker stop python.standard python.slim python.alpine
docker rm   python.standard python.slim python.alpine
🧠 Quick Reference Card

┌──────────────────────────────────────────────────────────────────────┐
│              DOCKER IMAGE TYPES — QUICK REFERENCE                    │
├──────────────┬──────────┬────────┬──────────┬────────────────────────┤
│ Type         │ Size     │ Shell  │ Pkg Mgr  │ Best For               │
├──────────────┼──────────┼────────┼──────────┼────────────────────────┤
│ Standard     │ 300MB+   │  ✅   │  ✅ apt  │ Development            │
│ Slim         │ 50-150MB │  ✅   │  ✅ apt  │ Python/Node Production │
│ Alpine       │ 5-50MB   │  ✅   │  ✅ apk  │ Go/Node/Simple scripts │
│ Distroless   │ 2-30MB   │  ❌   │  ❌      │ Secure Production      │
│ Scratch      │ 0 MB     │  ❌   │  ❌      │ Go/Rust Static Bins    │
└──────────────┴──────────┴────────┴──────────┴────────────────────────┘

🐍 Python  → SLIM        🐹 Go     → SCRATCH/DISTROLESS
🟢 Node.js → SLIM/ALPINE ♨️  Java   → eclipse-temurin:slim
👨‍💻 Author

Akshay Sawant AWS DevOps Engineer | AWS Solutions Architect Associate

Email Phone Location

⭐ If this helped you, give it a star! ⭐

Part of my DevOps learning series — see more projects on my GitHub


==========================================================================================================================
 🐳 Multi-Stage Dockerfile  🐳
==========================================================================================================================
⭐ Underastanding Compiled language VS Interpreted Languages.
 
🧪 Compiled language (Required Build tools)
- Source Code is translated into machine (binary)code before execution
- Requires the build stage with compiler and tools(maven ).
- Offers Faster Execution due to direct interaction with machine code.
- Ex: java, C/C++, Go...

🧪 Interpreted Languages 
- Source code is executed by an interpreter at runtime
- No separate build stage is generally required.
- Typically , slower execution due to the on-the-fly interpretation process.
- Ex: python,JavaScript, Ruby,PHP...

⭐ Importance:
- When building Docker image, instruction like FROM ,RUN, COPY and ADD create additional layers which increase the image size. Tools and dependencies used during build process often bloat the final size of image.

⭐Instruction that create and do not create Layers:

-Layer-Creating-Instructions- FROM, RUN, COPY , ADD.
-Non-Layer-Creating-Instructions- CMD, ENTRYPOINT, WORKDIR, EXPOSE, ENV, LABEL, USER, VOLUME, ARG.

⭐ Best pratices:
-Combine multiple commands into single RUN instruction (&&) to reduce the number of layers.(reduce size)
- RUN apt update -y && apt install git && apt install maven.

⭐  Use .dockerignore file to exclude unnecessary files from the build context.

🚀 A Multi-Stage Dockerfile is a dockerfile that uses multiple FROM instructions to define BUILD stage.
🚀 Each stage can have its own base image , dependencies and instructions.
🚀 In Multi-stage Dockerfile we will use one stage to BUILD the application ( which might require compiler , dependiences...) and another stage to RUN the application with ony the necessary artifacts.( Without the Build tools)
🚀 This makes the Dockerfile seperation of BUILD environment and RUNTIME environment.


==========================================================================================================================
🐳 Project Docker-3 🐳 (Multi-Stage Dockerfile Go Application)
==========================================================================================================================
This is the step by step approch


indrasurya@akshays-MacBook-Air docker % mkdir Go-App
indrasurya@akshays-MacBook-Air docker % ls
Go-App
indrasurya@akshays-MacBook-Air docker % cd Go-App
indrasurya@akshays-MacBook-Air Go-App % ls
indrasurya@akshays-MacBook-Air Go-App % vim main.go
indrasurya@akshays-MacBook-Air Go-App % ls
main.go
indrasurya@akshays-MacBook-Air Go-App % cat main.go
package main

import (
    "fmt"
    "net/http"
)

func main() {
    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        fmt.Fprintf(w, "Hello World from Go Web App!")
    })

    fmt.Println("Server running on port 8080")
    http.ListenAndServe(":8080", nil)
}
indrasurya@akshays-MacBook-Air Go-App %

we will  first write a single stage dockerfile and then multistage dockerfile and will see the size variation.
------------------------------------
Single stage Dockerfile
------------------------------------
FROM golang:1.21
WORKDIR /app
COPY . .
RUN go build -o hello main.go  (  hello (u can give any name for application) is output binary file , when this code is executed it will store all binary file in hello)
EXPOSE 8080
CMD ["./hello"]

indrasurya@akshays-MacBook-Air Go-App % vim dockerfile
indrasurya@akshays-MacBook-Air Go-App % cat dockerfile
FROM golang:1.21
WORKDIR /app
COPY . .
RUN go build -o hello main.go
EXPOSE 8080
CMD ["./hello"]
indrasurya@akshays-MacBook-Air Go-App % docker build -t go-single .

IMAGE                     ID             DISK USAGE   CONTENT SIZE   EXTRA
go-single:latest          dbda5957a9c3       1.29GB          306MB


------------------------------------
Now write a multi-stage dockerfile
------------------------------------

FROM golang:1.21 AS builder
WORKDIR /app
COPY . .
RUN CGO_ENABLED=o GOOS=linux go build -o hello main.go

FROM alpine:3.19
WORKDIR /app
COPY --form=builder /app/hello .   (It will copy the hello binary file from first stage which is in /app to second stage which is in /app folder)
EXPOSE 8080
CMD ["./hello"]

indrasurya@akshays-MacBook-Air Go-App % docker build -t go-multi .
IMAGE                     ID             DISK USAGE   CONTENT SIZE   EXTRA
go-multi:latest           99f3d38b60f6       21.9MB         7.04MB
go-single:latest          dbda5957a9c3       1.29GB          306MB

NOTE: Observe the size reduction when you use multistage dockerfile by using alpine image.

indrasurya@akshays-MacBook-Air Go-App % docker ps
CONTAINER ID   IMAGE       COMMAND     CREATED         STATUS         PORTS                                         NAMES
2bc41d60f9e8   go-multi    "./hello"   7 seconds ago   Up 6 seconds   0.0.0.0:8082->8080/tcp, [::]:8082->8080/tcp   go-multi-app
0b2932c5193d   go-single   "./hello"   9 minutes ago   Up 9 minutes   0.0.0.0:8081->8080/tcp, [::]:8081->8080/tcp   go-single-app
indrasurya@akshays-MacBook-Air Go-App %

🚀Run locally to check the application is running  in browser -->http://localhost:8081 ,  http://localhost:8082

FINAL NOTE: comapre the size of image is important. final solution of the project .

IMAGE                     ID             DISK USAGE   CONTENT SIZE   EXTRA
go-multi:latest           99f3d38b60f6       21.9MB         7.04MB
go-single:latest          dbda5957a9c3       1.29GB          306MB

------------------------------------------------------------------------------------------------------------------------------------------------------------
🐳 Project Docker-4 🐳 (Multi-Stage Dockerfile Java Application)
------------------------------------------------------------------------------------------------------------------------------------------------------------

indrasurya@akshays-MacBook-Air app-java % mkdir -p src/main/java/com/example/
indrasurya@akshays-MacBook-Air app-java % ls
pom.xml	src
indrasurya@akshays-MacBook-Air app-java % cd src/main/java/com/example
indrasurya@akshays-MacBook-Air example % ls
indrasurya@akshays-MacBook-Air example % vim DemoApplication.java
indrasurya@akshays-MacBook-Air example % cat DemoApplication.java
package com.example;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.*;

@SpringBootApplication
@RestController
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

    @GetMapping("/")
    public String home() {
        return "Hello from Multi-Stage Docker + Java!";
    }
}
indrasurya@akshays-MacBook-Air example % cd ../../..
indrasurya@akshays-MacBook-Air main % ls
java
indrasurya@akshays-MacBook-Air main % cd ..
indrasurya@akshays-MacBook-Air src % ls
main
indrasurya@akshays-MacBook-Air src % cd ..
indrasurya@akshays-MacBook-Air app-java % ls
pom.xml	src
indrasurya@akshays-MacBook-Air app-java %
indrasurya@akshays-MacBook-Air app-java % vim dockerfile.multisatge
indrasurya@akshays-MacBook-Air app-java %

--------------------------------------------
✅ Multi-Stage Dockerfile
--------------------------------------------
FROM maven:3.9.3-eclipse-temurin-17 AS builder
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN mvn clean package -DskipTests

FROM eclipse-temurin:17-jdk-alpine
WORKDIR /app
COPY --from-builder /app/target/demo-1.0.0.jar app.jar    (For version (demo-1.0.0.jar ) look in to pom.xml file ,It will copy the .jar file from first stage which is available in /app/target folder to second stage with renaming to app.jar  to current location /app)
EXPOSE 8080
ENTRYPOINT ["java","-jar","app.jar"]


-To know the which type of file (.tar,.war) is generated we need to look for pom.xml file -----> <packaging>jar<packaging>


NOTE: It will give ERROR if yu are using mac M Chip ---->
- indrasurya@akshays-MacBook-Air app-java % docker build -t java-multi-app -f dockerfile.multistage .
[+] Building 1.6s (3/3) FINISHED                                                 docker:desktop-linux
 => [internal] load build definition from dockerfile.multistage                                  0.0s
 => => transferring dockerfile: 338B                                                             0.0s
 => CANCELED [internal] load metadata for docker.io/library/openjdk:17-slim                      1.5s
 => ERROR [internal] load metadata for docker.io/library/maven:3.9.3-openjdk-17                  1.5s
------
 > [internal] load metadata for docker.io/library/maven:3.9.3-openjdk-17:
------
dockerfile.multistage:2
--------------------
   1 |     # Dockerfile.multistage - Using OpenJDK
   2 | >>> FROM maven:3.9.3-openjdk-17 AS builder
   3 |     WORKDIR /app
   4 |     COPY pom.xml .
--------------------
ERROR: failed to build: failed to solve: maven:3.9.3-openjdk-17: failed to resolve source metadata for docker.io/library/maven:3.9.3-openjdk-17: docker.io/library/maven:3.9.3-openjdk-17: not found

View build details: docker-desktop://dashboard/build/desktop-linux/desktop-linux/xpcvj7f12xrue2xbuxtizicv
---------------------------------------------------------------------------------------------------------------------------------------------------------


NOTE: If you are using the Apple Silicon Chip M1/M2/M3 use the below dockerfile

# Dockerfile.multistage - Verified working on Apple Silicon
FROM maven:3.9.3-amazoncorretto-17 AS builder
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN mvn clean package -DskipTests

FROM amazoncorretto:17-alpine
WORKDIR /app
COPY --from=builder /app/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]

IMAGE                     ID             DISK USAGE   CONTENT SIZE   EXTRA
java-multi-app:latest     23651a77762e        489MB          168MB

indrasurya@akshays-MacBook-Air app-java % docker run -d --name java-app -p 8080:8080 java-multi-app
a05f10c92b8e46480469ee8bea880cbdfd6f55015ddc4f81522afd2dcb02c95c
indrasurya@akshays-MacBook-Air app-java % docker ps
CONTAINER ID   IMAGE            COMMAND               CREATED         STATUS         PORTS                                         NAMES
a05f10c92b8e   java-multi-app   "java -jar app.jar"   8 seconds ago   Up 7 seconds   0.0.0.0:8080->8080/tcp, [::]:8080->8080/tcp   java-app
indrasurya@akshays-MacBook-Air app-java %

now check the application locally--->http://localhost:8080
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------
✅ Single-Stage Dockerfile
--------------------------------------------
FROM maven:3.9.3-eclipse-temurin-17

WORKDIR /app

# Copy source code
COPY pom.xml .
COPY src ./src

# Build the application
RUN mvn clean package -DskipTests

# Expose port
EXPOSE 8080

# Run the application
ENTRYPOINT ["java", "-jar", "/app/target/demo-1.0.0.jar"]

Build with single stage and absorve the image size ?
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

=======================================================================================================================================================================
🐳 Limitimg the Size of containers (memory limit)
=======================================================================================================================================================================

-To see the How much CPU and Memory the container is Using----> Docker stats <containerID)
- To limit the size for memory to new container -----> docker run -it --name <containername> --cpus="0.1" --memory="100MB" ubuntu -->This container cannot use CUP more than 0.1 core and memory= 100MB .
- If you want to increase the CPU or memory (increase or decrease) -----> docker update <containername> --cpus="0.5" --memory="200MB" , 
-NOTE : Problem with memory limit is once you set you can not increase the limit you can decrease but to able to increase. Advance concept in k8s is "Resource Quota"


=======================================================================================================================================================================
🐳 Docker Project-3 🐳 (Hotstar-App using Multi-stage Dockerfile)
=======================================================================================================================================================================
- git clone https://github.com/social9009/Docker-Project-3-CinemaWorld-Spring-boot-Java-Application.git


-For .jar (spring-boot  Java Application use below docker file)

# ─────────────────────────────────────────────────────────────────
# Multi-Stage Dockerfile — CinemaWorld Java Spring Boot App
# ─────────────────────────────────────────────────────────────────

# ── STAGE 1: BUILD ────────────────────────────────────────────────
# Full Maven + JDK image to compile and package the JAR
# This stage is ~600MB but NEVER ships to production
# ─────────────────────────────────────────────────────────────────
FROM maven:3.9.3-amazoncorretto-17 AS builder
WORKDIR /app

# Copy pom.xml first — Docker caches dependency download layer
# So if only src/ changes, Maven deps are NOT re-downloaded
COPY pom.xml .
RUN mvn dependency:go-offline -B

# Copy source code and build the JAR
COPY src ./src
RUN mvn clean package -DskipTests

# ─────────────────────────────────────────────────────────────────
# ── STAGE 2: RUN ─────────────────────────────────────────────────
# Minimal Alpine + Amazon Corretto JRE
# Only the compiled JAR from Stage 1 is copied here
# ─────────────────────────────────────────────────────────────────
FROM amazoncorretto:17-alpine
WORKDIR /app

# Copy ONLY the JAR from Stage 1 — Maven and source code stay behind
COPY --from=builder /app/target/*.jar app.jar

EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]





------------------------------------------------------------------------
-For .war ( Normal Java  Application use below docker file)
------------------------------------------------------------------------

FROM maven:3.9.6-eclipse-trmurin-17 AS builder
WORKDIR /app
COPY . .
RUN mvn clean package -DskipTests

FROM tomcat:9.0-jdk17
RUN rm -rf /usr/local/tomcat/webapps/*
COPY --from=builder /app/target/myapp.war /usr/local/tomcat/webapps/ROOT.war
EXPOSE 8080
CMD ["catalina.sh","run"].    (# catalina.sh ensure it will run in Foreground Mode)


--------------------------------------------------------------------------------------------------------------------------
🐳 Docker-Project-5. ----->SpringBoot Java Application 
--------------------------------------------------------------------------------------------------------------------------
Normal Java Application---> .War 
SpringBoot Java Applicaton-----> .Jar


🏠 Casa Royale — Luxury PG Marketing Website | Docker Multi-Stage Build

Docker Java Spring Boot Maven Alpine


A luxury marketing website for Casa Royale — India's First Luxury PG & Co-Living Space in Hinjewadi, Pune. Built with Java Spring Boot and containerized using Docker Multi-Stage Build.


Designed & Developed by Akshay Sawant — AWS DevOps Engineer | Hinjewadi, Pune

🌐 Run Locally → Open http://localhost:8080

Contact	Details
📞 Phone	+91 86240 86240
📸 Instagram	@casaroyale_pg
📍 Location	Hinjewadi & Marunji, Pune
💬 WhatsApp	Chat Now
📌 Table of Contents

Project Overview
About Casa Royale
Website Sections
Project Structure
Tech Stack
Multi-Stage Dockerfile Explained
Step-by-Step Commands
Docker Size Comparison
Clone and Run
Push to GitHub
Key Learnings
🎯 Project Overview

This is Docker Project 5 in Akshay Sawant's Docker learning series.

What this project demonstrates:

Docker Multi-Stage Build with Java Spring Boot — ships only the compiled JAR in minimal Alpine (no Maven in production)
Full luxury marketing website — served via Spring Boot's static file hosting
Real business content — all photos, video tour, contact details, WhatsApp/Instagram links for Casa Royale PG
Production-ready Docker image — run with a single command
🏠 About Casa Royale

India's First Luxury PG — OXYGEN PLUS
Casa Royale is a premium co-living and PG accommodation located in Hinjewadi IT Hub, Pune — the heart of Pune's technology corridor. It's designed specifically for students and working professionals who want more than just a place to sleep.

✅ 33+ Premium Amenities
✅ 21-Day Non-Repeating Meal Plan (Mama's Stove Restaurant)
✅ 7-Step Hygiene Routine
✅ 5-Tier Safety System
✅ 1,200+ Indoor Plants
✅ Private Balconies
✅ Billiards + Foosball Tables
✅ Co-Working Spaces
✅ Marble Corridors
✅ Biometric Entry + CCTV
Location:

Opposite Prathamshree Vatika,
Marunji Road, Hinjewadi Phase 1,
Pune – 411057, Maharashtra
Contact:

📞 +91 86240 86240
📸 Instagram: @casaroyale_pg
💬 WhatsApp: wa.me/918624086240
🌐 Website Sections

The marketing website contains the following fully designed sections:

Section	Description
🎬 Hero	Full-screen video tour with animated stats counter and booking CTA
🏠 About	India's First Luxury PG story with building photo and feature list
⭐ Amenities	All 33+ amenities in an animated hover grid (icons + names)
📸 Gallery	20 real photos with filter tabs: Rooms, Dining, Common, Exterior
🎥 Video Tour	Compiled slideshow video from all property photos
🍽️ Meals	Mama's Stove section with 21-day meal plan details
🔒 Safety	5-Tier Safety System with CCTV, biometric, guards
🛏️ Rooms	Three room types with photos and features
🪴 Plants	1,200+ indoor plants with giant animated number
⭐ Testimonials	Auto-scrolling resident review cards
📞 Contact	Phone, WhatsApp, Instagram, address, map, enquiry form
🦶 Footer	Full footer with links and brand info
Design Features:

🌑 Deep luxury dark green/gold theme
✨ 80 animated star particles in background
⚡ Subtle lightning flash effect
🖱️ Custom golden cursor with ring
🎨 Gold + Forest Green color palette
📱 Fully responsive (mobile, tablet, desktop)
🔄 Scroll-reveal animations on all sections
💬 WhatsApp enquiry form (pre-filled message)
📸 Floating Instagram + WhatsApp buttons
🎞️ Auto-play video hero with fallback image
📁 Project Structure

casa-royale/
│
├── 📄 README.md                               ← This documentation
├── 📄 pom.xml                                 ← Maven (outputs casa-royale-1.0.0.jar)
├── 📄 dockerfile                              ← Multi-Stage (Apple M1/M2/M3 ✅)
├── 📄 dockerfile.intel                        ← Multi-Stage (Intel/AMD64/Linux ✅)
├── 📄 .gitignore
│
└── 📁 src/
    └── 📁 main/
        ├── 📁 java/com/akshay/casaroyale/
        │   └── 📄 CasaRoyaleApp.java          ← Spring Boot main class + /health API
        └── 📁 resources/
            └── 📁 static/
                ├── 📄 index.html              ← Full luxury marketing website
                ├── 📁 images/                 ← All 20 property photos
                │   ├── building-night.jpg
                │   ├── building-day.jpg
                │   ├── room1.jpg
                │   ├── lounge.jpg
                │   ├── dining-full.jpg
                │   ├── restaurant-door.jpg
                │   ├── pool-table.jpg
                │   ├── billiards.jpg
                │   ├── foosball.jpg
                │   ├── staircase.jpg
                │   ├── corridor.jpg
                │   ├── buffet.jpg
                │   ├── food1.jpg
                │   ├── food2.jpg
                │   ├── dining1.jpg
                │   ├── dining2.jpg
                │   ├── dining3.jpg
                │   ├── common-area.jpg
                │   ├── building-ad.jpg
                │   └── brand.png
                └── 📁 video/
                    └── 🎬 casa-royale-tour.mp4 ← Auto-generated slideshow video
🛠️ Tech Stack

Layer	Technology	Purpose
Backend	Spring Boot 3.1, Java 17	Serves website, handles /health endpoint
Frontend	HTML5, CSS3, Vanilla JS	Luxury marketing website UI
Build	Maven 3.9	Compiles and packages to JAR
Container (Build)	maven:3.9.3-amazoncorretto-17	Full Maven + JDK for compilation only
Container (Run)	amazoncorretto:17-alpine	Minimal Alpine + JRE for production
Video	FFmpeg	Combines property photos into tour video
Fonts	Google Fonts (Cormorant Garamond, Montserrat, Playfair Display)	Luxury typography
Icons	Font Awesome 6.5	UI icons throughout
🐳 Multi-Stage Dockerfile Explained

# ── STAGE 1: BUILD ──────────────────────────────────────────────
# Full Maven image — compiles the Spring Boot app into a JAR
# This ~600MB image is NEVER shipped to production
FROM maven:3.9.3-amazoncorretto-17 AS builder
WORKDIR /app

# Smart caching: pom.xml copied FIRST
# If only Java code changes, Maven deps are NOT re-downloaded
COPY pom.xml .
RUN mvn dependency:go-offline -B      ← pre-download all dependencies

COPY src ./src
RUN mvn clean package -DskipTests     ← compile → produces JAR in target/

# ── STAGE 2: RUN ───────────────────────────────────────────────
# Only minimal Alpine JRE + your compiled JAR
# Everything else from Stage 1 is automatically discarded
FROM amazoncorretto:17-alpine
WORKDIR /app
COPY --from=builder /app/target/*.jar app.jar   ← only the JAR crosses over
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
Why This Matters:

WITHOUT Multi-Stage:
  Image = Maven (~400MB) + JDK (~300MB) + source code + JAR + static files
  Total: 700MB+ — unnecessary build tools ship to production ❌

WITH Multi-Stage:
  Stage 1 builds everything → Stage 2 gets only the JAR
  Image = Alpine JRE (~200MB) + JAR (~15MB) + static files
  Total: ~320MB — lean, secure, production-ready ✅
💻 Step-by-Step Commands

Step 1 — Create Project Structure

mkdir casa-royale
cd casa-royale
mkdir -p src/main/java/com/akshay/casaroyale
mkdir -p src/main/resources/static/images
mkdir -p src/main/resources/static/video
Step 2 — Add All Files

# Place all files as shown in Project Structure above
ls src/main/resources/static/images/
# building-night.jpg  room1.jpg  lounge.jpg  dining-full.jpg ... (20 images)
ls src/main/resources/static/video/
# casa-royale-tour.mp4
Step 3 — Build Docker Image (Apple M1/M2/M3)

docker build -t casa-royale -f dockerfile .
Step 3 — Build Docker Image (Intel/Linux)

docker build -t casa-royale -f dockerfile.intel .
Step 4 — Check Image Size

docker images casa-royale
IMAGE                  DISK USAGE    CONTENT SIZE
casa-royale:latest       ~320 MB       ~160 MB
Step 5 — Run Container

docker run -d --name casa-royale-app -p 8080:8080 casa-royale
Step 6 — Verify Running

docker ps
# CONTAINER ID   IMAGE          COMMAND               PORTS
# abc12345       casa-royale    "java -jar app.jar"   0.0.0.0:8080->8080/tcp
Step 7 — Test

# Health check
curl http://localhost:8080/health
# → Casa Royale PG — Luxury Living is LIVE! 🏠✨

# Open website
open http://localhost:8080
Step 8 — Clean Up

docker stop casa-royale-app
docker rm   casa-royale-app
📊 Docker Size Comparison

Build Approach     Base Images                        Final Size
─────────────────────────────────────────────────────────────────
Single-Stage  →   maven:3.9.3-amazoncorretto-17       ~700 MB  ❌
Multi-Stage   →   amazoncorretto:17-alpine             ~320 MB  ✅
─────────────────────────────────────────────────────────────────
              Savings: ~380 MB  (54% smaller)
📥 Clone and Run

# Clone
git clone https://github.com/social9009/casa-royale.git
cd casa-royale

# Build (Apple M1/M2/M3)
docker build -t casa-royale -f dockerfile .

# Build (Intel/Linux)
docker build -t casa-royale -f dockerfile.intel .

# Run
docker run -d --name casa-royale-app -p 8080:8080 casa-royale

# Open
open http://localhost:8080
🚀 Push to GitHub

cd casa-royale
git init
git add .
git commit -m "feat: casa royale luxury PG marketing website - Spring Boot + Docker multi-stage"
git remote add origin https://github.com/social9009/casa-royale.git
git branch -M main
git push -u origin main
💡 Key Learnings

✅ Docker Multi-Stage for Java

Stage 1: Maven compiles .java → .jar  (~15MB output)
Stage 2: Alpine JRE runs the .jar     (~200MB base)
= 320MB final vs 700MB single-stage  (54% smaller)
✅ Spring Boot Static File Serving

Place HTML/CSS/JS/images in:
  src/main/resources/static/

Spring Boot auto-serves them at root URL:
  http://localhost:8080/           → index.html
  http://localhost:8080/images/x.jpg → images/x.jpg
  http://localhost:8080/video/x.mp4  → video/x.mp4

No extra configuration needed! Spring Boot does it automatically.
✅ Smart Docker Layer Caching

# Copy pom.xml FIRST → deps cached if only code changes
COPY pom.xml .
RUN mvn dependency:go-offline    ← CACHED on rebuild
COPY src ./src                   ← only this layer re-runs on code change
RUN mvn clean package
🔗 Related Docker Projects

Docker-4 Docker-3 Docker-2 SonarQube

🏠 Casa Royale Quick Info

Property Name : Casa Royale — OXYGEN PLUS
Type          : Luxury PG & Co-Living Space
Location      : Hinjewadi Phase 1 & Marunji, Pune
Address       : Opp. Prathamshree Vatika, Marunji Road, Pune 411057
Phone         : +91 86240 86240
WhatsApp      : wa.me/918624086240
Instagram     : @casaroyale_pg
Amenities     : 33+
Meal Plan     : 21-Day Non-Repeating (Mama's Stove)
Safety        : 5-Tier System
Plants        : 1,200+ Indoor Plants
Ideal For     : IT Professionals & Students in Hinjewadi
👨‍💻 Developer

Akshay Sawant — AWS DevOps Engineer | AWS Solutions Architect Associate

Email Phone GitHub Location

⭐ Star this repo if you found it useful! ⭐

Docker Series: Image Types → Python → Go Multi-Stage → Java CinemaWorld → Java Casa Royale (this)

------------------------------------------------------------------------------------------------------------------------
Docker-Compose 
------------------------------------------------------------------------------------------------------------------------
-Docker-compose.yml------> Service Information----> 
service name---> 
build--> configure the path of dockerfile
image---> Image Name, Version, (If image already available in the local)
container----> container information (name)
port----> port mapping
network---> network information


IMP:
🔹 1. docker compose up -d
-This command , starts the containers in detached but it doesnot rebuild the images, it will always use already-built images from  the previous builds ,If image is not available , it will build once and then starts container ,if image is already available , it will directly use those image and run the container 

✅ Behavior
Starts containers in detached mode
Uses existing images
Does NOT rebuild images even if Dockerfile changed
🔍 What happens internally
Checks if containers already exist
If not → creates them using existing images
If yes → starts/recreates only if config changed
⚡ Use case
Fast startup when no code or Dockerfile changes
Production or stable environments
🧠 Example
docker compose up -d
👉 If your app code changed but image not rebuilt → changes won’t reflect


🔹 2. docker compose up -d --build
- Rebuilds the all images , then it will start containers in detached mode.
✅ Behavior
Forces image rebuild before starting containers
Then runs containers in detached mode
🔍 What happens internally
Runs docker compose build
Rebuilds images from Dockerfile
Then runs docker compose up -d
⚡ Use case
When:
Code changed
Dependencies updated
Dockerfile modified
🧠 Example
docker compose up -d --build
👉 Ensures latest code is inside container

🔥 Key Difference (Side-by-Side)
| Feature               | `docker compose up -d`    | `docker compose up -d --build` |
| --------------------- | ------------------------- | ------------------------------ |
| Builds image          | ❌ No                      | ✅ Yes                          |
| Uses cache            | ✅ Yes                     | ✅ Yes (unless no-cache used)   |
| Reflects code changes | ❌ No (if image unchanged) | ✅ Yes                          |
| Speed                 | ⚡ Faster                  | 🐢 Slower (build time)         |
| When to use           | No changes                | After changes                  |

------------------------------------------------------------------------------------------------------------------------
Docker Application ----> Frontend + Backend(Database)
------------------------------------------------------------------------------------------------------------------------


------------------------------------------------------------------------------------------------------------------------
Project 6 | 5 Microservices | Docker Compose | AWS Cloud | GitHub Ready | Real-Time Industry Level
------------------------------------------------------------------------------------------------------------------------

------------------------------------------------------------------------------------------------------------------------
🔍 .dockerignore  file— Deep DevOps Explanation
------------------------------------------------------------------------------------------------------------------------
A .dockerignore file is conceptually similar to .gitignore, but it operates in the Docker build context pipeline. It tells Docker which files/directories should NOT be sent to the Docker daemon during docker build.

--->.dockerignore is a text file placed at the root of docker build context.
---> .dockerignore prevents unnecessary files from being sent to the build context.

🧠 Why .dockerignore Exists (Core Problem)
When you run:
docker build -t myapp .
Docker does not just read your Dockerfile — it sends the entire directory (build context) to the Docker daemon.
👉 This includes:
source code
logs
.git history
node_modules
secrets
temporary files
⚠️ Without .dockerignore, this leads to:
Huge build context size
Slow builds
Security risks (leaking secrets)
Inefficient caching
🎯 Purpose of .dockerignore
| Objective               | Explanation                                 |
| ----------------------- | ------------------------------------------- |
| 🚀 Optimize build speed | Smaller context → faster transfer to daemon |
| 🔐 Improve security     | Prevent secrets from entering image         |
| 📦 Reduce image size    | Avoid unnecessary layers                    |
| ♻️ Better caching       | Stable builds when irrelevant files change  |


⚙️ How .dockerignore Works
File name: .dockerignore
Location: same directory as Dockerfile
Syntax: similar to .gitignore
Example:
# Ignore Git files
.git
.gitignore

# Ignore logs
*.log

# Ignore dependencies
node_modules

# Ignore env files
.env

# Ignore OS files
.DS_Store


🔥 Real-Time DevOps Use Case (Production Scenario)
🏢 Scenario: Microservices App with CI/CD Pipeline
You are building a Dockerized Flask + MySQL app (like your current project).
Project structure:
project/
│── backend/
│── frontend/
│── node_modules/
│── logs/
│── .env
│── Dockerfile

❌ Without .dockerignore
Sending build context to Docker daemon  850MB
Problems:
CI pipeline slow (Jenkins/GitHub Actions)
.env secrets copied into image ❗
node_modules copied (even if rebuilt inside container)
✅ With .dockerignore
node_modules
.env
logs
.git
*.log
Now:
Sending build context to Docker daemon  45MB
✔ Faster builds
✔ Secure image
✔ Cleaner layers
🧪 DevOps Pipeline Impact
In CI/CD (e.g., GitHub Actions / Jenkins)
Without .dockerignore:
Longer pipeline execution
Increased bandwidth usage
Cache invalidation on every minor change
With .dockerignore:
Efficient layer caching
Faster deployment cycles
Reduced infrastructure cost
🔐 Security Use Case (Very Important)
❌ Bad Practice
If .env is included:
DB_PASSWORD=root123
AWS_SECRET_KEY=XXXX
👉 These get baked into the Docker image → security breach
✅ Fix using .dockerignore
.env
secrets/
*.pem
⚡ When to Use .dockerignore
Use it ALWAYS when building Docker images, especially:
Microservices architecture
CI/CD pipelines
Production deployments
Large repositories
Projects with secrets/config files
🧰 Advanced Patterns
1. Ignore everything except specific files
*
!app.py
!requirements.txt
2. Ignore nested directories
**/node_modules
3. Ignore build artifacts
dist/
build/
📊 .dockerignore vs .gitignore

| Feature       | `.dockerignore`      | `.gitignore`    |
| ------------- | -------------------- | --------------- |
| Scope         | Docker build context | Git repo        |
| Purpose       | Optimize builds      | Version control |
| Security role | High                 | Medium          |
| Affects image | Yes                  | No              |

🧠 Best Practices (Industry Level)
Always create .dockerignore before writing Dockerfile
Keep it minimal but effective
Exclude:
.git
.env
logs
temp files
dependencies (if rebuilt inside container)
Validate build context:
docker build . --no-cache
🧩 Summary
.dockerignore is a performance + security optimization tool in Docker builds.
Reduces build context size
Speeds up CI/CD pipelines
Prevents sensitive data leaks
Improves caching efficiency

--------------------------------------------------------------------------------------------------------------------------
🔐 Pushing to a Private Docker Repository (DevOps Perspective) --------------------------------------------------------------------------------------------------------------
Instead of password we will configure PAT (Personal Accesses Token)

In the processes of generating the PAT , we will have 4 options in permission
1- Public-repo read-only----> can only pull public images
2- Read-only -----> can only pull both public + private images
3- Read and write ----> can pull + push the image (public+private)
4- Read,  write and Delete --> pull,push, delete the images /tags(Admin tasks)

🧠 Why Use Private Repository?
| Reason               | Explanation                    |
| -------------------- | ------------------------------ |
| 🔐 Security          | Store proprietary code/images  |
| 🚀 CI/CD             | Used in deployment pipelines   |
| 📦 Versioning        | Manage image versions          |
| 🌍 Controlled Access | Only authorized users/services |

⚙️ Step-by-Step (Docker Hub Private Repo)
1️⃣ Create Private Repo
Go to Docker Hub
Create repository
Select Private
2️⃣ Login to Docker
docker login
👉 Enter:
Username
Password / Access Token (recommended)
3️⃣ Tag Your Image
docker tag myapp:latest username/myapp:v1
📌 Format:
<dockerhub-username>/<repo-name>:<tag>
4️⃣ Push Image
docker push username/myapp:v1
5️⃣ Verify
Check in Docker Hub → repository → tags
🔥 Real-Time DevOps Scenario
🏢 Scenario: CI/CD Pipeline (GitHub Actions)
You build image → push to private repo → deploy to server
# GitHub Actions example
- name: Login to Docker Hub
  run: echo ${{ secrets.DOCKER_PASSWORD }} | docker login -u ${{ secrets.DOCKER_USERNAME }} --password-stdin

- name: Build Image
  run: docker build -t myapp .

- name: Tag Image
  run: docker tag myapp ${{ secrets.DOCKER_USERNAME }}/myapp:v1

- name: Push Image
  run: docker push ${{ secrets.DOCKER_USERNAME }}/myapp:v1
☁️ AWS ECR (Industry Standard)
Using Amazon Elastic Container Registry
Login
aws ecr get-login-password --region ap-south-1 | \
docker login --username AWS --password-stdin <account-id>.dkr.ecr.ap-south-1.amazonaws.com
Tag Image
docker tag myapp:latest <account-id>.dkr.ecr.ap-south-1.amazonaws.com/myapp:v1
Push
docker push <account-id>.dkr.ecr.ap-south-1.amazonaws.com/myapp:v1
🔐 Security Best Practices
❌ Don’t Do This
Hardcode passwords in scripts
Use root credentials
Push images with secrets inside
✅ Do This
Use access tokens instead of passwords
Store secrets in:
GitHub Secrets
AWS Secrets Manager
Use .dockerignore to exclude:
.env
secrets/

⚠️ Common Errors & Fixes
| Error                       | Reason            | Fix               |
| --------------------------- | ----------------- | ----------------- |
| `denied: requested access`  | Not logged in     | `docker login`    |
| `repository does not exist` | Wrong repo name   | Create repo first |
| `unauthorized`              | Wrong credentials | Use token         |
| `no basic auth credentials` | Expired login     | Re-login          |

🚀 Advanced DevOps Flow
Developer → Git Push → CI/CD Pipeline →
Build Image → Tag → Push to Private Registry →
Kubernetes pulls image → Deploy
🧩 Pro Tip (Interview Level)
“In production, we never deploy from local images. We always push to a private registry like ECR or Docker Hub private repo, and deployment systems (Kubernetes/EC2) pull from there using IAM roles or secure credentials.”

------------------------------------------------------------------------------------------------------------------------
Mutable vs Immutable Containers 🔄
--------------------------------------------------------------------------------------------------------------------------
A mutable containers is the one where you start a container , make changes inside it directly, usecase: production, Debugging

A Immutable containers means you can never change the configuration /file  once the container is in running state.

--------------------------------------------------------------------------------------------------------------------------
🌐 Docker Networking — Complete DevOps Explanation
--------------------------------------------------------------------------------------------------------------------------
- Docker networking is used to make the communication between the multiple containers that are running on same host or on differnt docker hosts.
- Each container connects to one or more networks.
- Networks are isolated- containers which are running on different network cannot talk,by Default -unless you configure it.
- Docker provides different network drivers for different usecase.

🧠 Why Docker Networking is Needed
In real-world DevOps systems:
You don’t run a single container
You run multiple microservices
Example:
Frontend → Backend → Database → Cache → Message Queue
👉 These services must talk to each other securely and efficiently

🎯 Purpose of Docker Networking
| Purpose                          | Explanation                       |
| -------------------------------- | --------------------------------- |
| 🔗 Inter-container communication | Connect microservices             |
| 🌍 External access               | Expose services to users          |
| 🔐 Isolation                     | Secure communication              |
| ⚙️ Service discovery             | Use container names instead of IP |
| 📦 Scalability                   | Handle multiple replicas          |

⚙️ Types of Docker Networks

1️⃣ Bridge Network (Default)-->If 2 conatiners which are on same host wants to communicate.
2️⃣ Host Network----> If you want to use HOST network VPC of EC2 instance.
3️⃣ None Network----> If you want to expose any application
4️⃣ Overlay Network (Multi-Host)----> If 2 containers which are on different host want to communicate with each other.
5️⃣ Macvlan Network-----> It attach conatiners directly to physical network ,Gives containers their own MAC addresses on the physical network.

🧠 Interview-Level Answer (Strong Statement)
“Docker networking enables containerized microservices to communicate using isolated, software-defined networks. In production, we typically use user-defined bridge networks for single-host setups and overlay networks for multi-host orchestration like Kubernetes. This ensures secure, scalable, and DNS-based service discovery across containers.”
🧩 Summary
Docker networking = backbone of microservices communication
Enables service-to-service interaction
Provides security, isolation, scalability
Critical for CI/CD + Kubernetes deployments

------------------------------------------------------------------------------------------------------------------------
🚀 Install Docker on Ubuntu (Latest)
------------------------------------------------------------------------------------------------------------------------

1. Update system packages
sudo apt update
sudo apt upgrade -y

2. Install required dependencies
sudo apt install -y ca-certificates curl gnupg lsb-release

3. Add Docker’s official GPG key
sudo mkdir -p /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

4. Set up Docker repository
echo \
"deb [arch=$(dpkg --print-architecture) \
signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

5. Install Docker Engine
sudo apt update

sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

6. Verify installation
docker --version
Test run:
sudo docker run hello-world
🔧 Post-Installation (Important for DevOps)
Run Docker without sudo
sudo usermod -aG docker $USER
newgrp docker
Enable Docker service at boot
sudo systemctl enable docker
sudo systemctl start docker
Check status:
sudo systemctl status docker
⚡ Real-time DevOps Notes
Why NOT use apt install docker.io?
Ubuntu repo version is outdated
Missing latest features like:
BuildKit
Compose V2 plugin
Security patches
Where this matters in real projects:
CI/CD pipelines (Jenkins, GitLab)
Kubernetes node setup
Microservices deployments
Docker Compose-based environments

🔍 Common Issues & Fixes
1. Permission denied error
Got permission denied while trying to connect to the Docker daemon
✔ Fix:
sudo usermod -aG docker $USER
newgrp docker

2. Docker service not running
sudo systemctl start docker

3. Port conflicts (DevOps frequent issue)
Check:
sudo lsof -i :<port>

✅ Optional (but recommended)
Install Docker Compose CLI (already included above)
Check:
docker compose version
📌 Summary (Quick Commands)
sudo apt update && sudo apt install -y ca-certificates curl gnupg lsb-release

sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo usermod -aG docker $USER
newgrp docker

---------------------------------------------------------------------------------------------------------------------
For Jenkins to communicate with Docker, the core requirement is simple but critical:
Jenkins must be able to access the Docker daemon (/var/run/docker.sock).
Everything revolves around permissions + environment setup.
---------------------------------------------------------------------------------------------------------------------


🔑 Core Commands (Must Know)
1. Add Jenkins user to Docker group
sudo usermod -aG docker jenkins
👉 This is the MOST IMPORTANT command
Why?
Docker daemon runs as root
Jenkins runs as jenkins user
Without this → ❌ permission denied
2. Restart Jenkins service
sudo systemctl restart jenkins
👉 Required because group changes don’t apply immediately
3. Verify Docker access from Jenkins user
sudo su - jenkins
docker ps
✔ If this works → integration is correct
❌ If error → permission issue

---------------------------------------------------------------------------------------------------------------------
