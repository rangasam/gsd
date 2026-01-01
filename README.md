# Getting Started with Docker - Lab Workbook

Official repo for Getting Started with Docker video training course by [@rangasam](https://twitter.com/rangasam)

## 📋 Course Overview

This repository contains hands-on labs designed to teach Docker fundamentals through practical exercises. You'll learn containerization concepts by building and deploying web applications using single containers, Docker Compose, and Docker Swarm.

### Learning Objectives

By completing these labs, you will:

- ✅ Understand Docker containers and images
- ✅ Build containerized applications from scratch
- ✅ Use Docker Compose for multi-container applications
- ✅ Deploy applications using Docker Swarm
- ✅ Manage container orchestration and scaling
- ✅ Work with volumes, networks, and container lifecycle

---

## 🎯 Lab Structure

### Lab 1: Single Container Web Application (`first-container`)

**Objective:** Build and run a simple Node.js web application in a Docker container

**What you'll build:** An Express.js web app with Handlebars templating that displays the container hostname

**Technologies:** Node.js, Express, Handlebars, Docker

- Docker hub image: [rangasam/gsd:first-ctr](https://hub.docker.com/repository/docker/rangasam/gsd)

#### Lab 1 Tasks:

**Task 1.1: Examine the Application Code**
1. Navigate to the `first-container` directory
2. Review `app.js` to understand the Express application structure
3. Examine `package.json` for dependencies
4. Check the `views` and `static` directories for templates and assets

**Task 1.2: Build the Docker Image**
1. Review the `Dockerfile` and understand each instruction:
   - Base image selection (`node:current-alpine`)
   - Working directory setup
   - Dependency installation
   - Application copying
   - Entry point configuration
2. Build the Docker image:
   ```bash
   cd first-container
   docker build -t gsd:first-container .
   ```
3. Verify the image was created:
   ```bash
   docker images | grep gsd
   ```

**Task 1.3: Run and Test the Container**
1. Run the container with port mapping:
   ```bash
   docker run -d -p 8080:8080 --name my-first-container gsd:first-container
   ```
2. Verify the container is running:
   ```bash
   docker ps
   ```
3. Test the application:
   ```bash
   curl http://localhost:8080
   # OR open http://localhost:8080 in your browser
   ```
4. Check container logs:
   ```bash
   docker logs my-first-container
   ```

**Task 1.4: Container Management**
1. Execute commands inside the running container:
   ```bash
   docker exec -it my-first-container sh
   # Explore the container filesystem
   # Check running processes: ps aux
   # Exit the container: exit
   ```
2. Stop and remove the container:
   ```bash
   docker stop my-first-container
   docker rm my-first-container
   ```

**Validation Checklist:**
- [ ] Container builds successfully without errors
- [ ] Application responds on http://localhost:8080
- [ ] Page displays "Have a blast learning Docker!" message
- [ ] Page shows the container hostname
- [ ] Container logs show the application starting correctly

---

### Lab 1.5: Enhanced Container Build (`container`)

**Objective:** Build and deploy an enhanced Node.js web application with updated dependencies

**What you'll build:** An improved Express.js web app with modern Handlebars configuration showing "WebAssembly is the future!"

**Technologies:** Node.js, Express, Handlebars (updated), Docker

- Docker hub image: [rangasam/gsd:ctr2023](https://hub.docker.com/repository/docker/rangasam/gsd)

#### Lab 1.5 Tasks:

**Task 1.5.1: Directory Navigation and Verification**
1. Verify Docker installation from the root directory:
   ```bash
   docker --version
   ```
   Expected output: `Docker version 28.0.4, build b8034c0`

2. Attempt to build from root directory (will show error - demonstrates importance of correct directory):
   ```bash
   docker image build -t rangasam/gsd:ctr2023 .
   ```
   Expected error: `ERROR: failed to solve: failed to read dockerfile: open Dockerfile: no such file or directory`

3. List directories to locate the correct lab directory:
   ```bash
   ll
   # OR
   ls -la
   ```
   You should see directories including: `container`, `first-container`, `compose`, etc.

**Task 1.5.2: Navigate to Container Directory and Build**
1. Navigate to the container directory:
   ```bash
   cd container
   ```

2. Verify Docker version from the correct directory:
   ```bash
   docker --version
   ```
   Output: `Docker version 28.0.4, build b8034c0`

3. Build the Docker image successfully:
   ```bash
   docker image build -t rangasam/gsd:ctr2023 .
   ```

**Task 1.5.3: Analyze the Successful Build Process**
The successful build demonstrates:
- Build time: ~2.8s (much faster due to layer caching)
- Base image: `node:current-alpine` with cached layers
- Build context: 1.22kB transferred
- Cached dependency installation (`npm install`)
- Final image tagged as `rangasam/gsd:ctr2023`

**Build Output Analysis:**
```
[+] Building 2.8s (11/11) FINISHED
 => [internal] load build definition from Dockerfile
 => [internal] load metadata for docker.io/library/node:current-alpine
 => [auth] library/node:pull token for registry-1.docker.io
 => [internal] load .dockerignore
 => [1/5] FROM docker.io/library/node:current-alpine@sha256:f4769...
 => [internal] load build context
 => CACHED [2/5] RUN mkdir -p /usr/src/app
 => CACHED [3/5] COPY . /usr/src/app
 => CACHED [4/5] WORKDIR /usr/src/app
 => CACHED [5/5] RUN npm install
 => exporting to image
```

**Task 1.5.4: Test the Enhanced Container**
1. Run the container:
   ```bash
   docker run -d -p 8080:8080 --name enhanced-container rangasam/gsd:ctr2023
   ```

2. Test the application:
   ```bash
   curl http://localhost:8080
   # OR open http://localhost:8080 in your browser
   ```

3. Verify the updated message displays: "WebAssembly is the future!"

**Key Learning Points:**
- **Directory Context Matters**: Docker build must be run from the directory containing the Dockerfile
- **Layer Caching**: Subsequent builds are faster due to Docker's layer caching mechanism
- **Build Context**: Docker builds include the current directory context (1.22kB in this case)
- **Error Debugging**: Understanding error messages helps troubleshoot build issues

**Validation Checklist:**
- [ ] Docker version 28.0.4 confirmed from both root and container directory
- [ ] Directory error demonstrated from root directory
- [ ] Successful navigation to `container` directory
- [ ] Image builds successfully in ~2.8 seconds
- [ ] Build utilizes layer caching (CACHED steps visible)
- [ ] Image tagged as `rangasam/gsd:ctr2023`
- [ ] Application displays "WebAssembly is the future!" message
- [ ] Container responds on http://localhost:8080

---

### Lab 2: Multi-Container Application with Docker Compose (`compose`)

**Objective:** Deploy a Flask application with Redis backend using Docker Compose

**What you'll build:** A Python Flask counter app that tracks page visits using Redis for persistence

**Technologies:** Python, Flask, Redis, Docker Compose

- Docker hub image: [rangasam/gsd:compose-app](https://hub.docker.com/repository/docker/rangasam/gsd)

#### Lab 2 Tasks:

**Task 2.1: Analyze the Multi-Container Architecture**
1. Navigate to the `compose` directory
2. Review `app/app.py` to understand:
   - Flask application structure
   - Redis connection and retry logic
   - Hit counter implementation
3. Examine `compose.yaml` to understand:
   - Service definitions (web-fe, redis)
   - Network configuration (counter-net)
   - Volume management (counter-vol)
   - Port mapping strategy

**Task 2.2: Build and Deploy with Docker Compose**
1. Review the `Dockerfile` for the Flask application
2. Build and start the multi-container application:
   ```bash
   cd compose
   docker-compose up --build -d
   ```
3. Verify all services are running:
   ```bash
   docker-compose ps
   ```
4. Check the created networks and volumes:
   ```bash
   docker network ls | grep compose
   docker volume ls | grep compose
   ```

**Task 2.3: Test the Application**
1. Access the application:
   ```bash
   curl http://localhost:5001
   # OR open http://localhost:5001 in your browser
   ```
2. Refresh the page multiple times and observe the counter increment
3. Check service logs:
   ```bash
   docker-compose logs web-fe
   docker-compose logs redis
   ```

**Task 2.4: Scale and Manage Services**
1. Scale the web frontend service:
   ```bash
   docker-compose up --scale web-fe=3 -d
   ```
2. Verify multiple web containers are running:
   ```bash
   docker-compose ps
   ```
3. Test load distribution (refresh browser multiple times)
4. View Redis data persistence:
   ```bash
   docker-compose exec redis redis-cli
   # In Redis CLI: GET hits
   # Exit: quit
   ```

**Task 2.5: Update and Restart Services**
1. Stop the services:
   ```bash
   docker-compose down
   ```
2. Restart with volume persistence:
   ```bash
   docker-compose up -d
   ```
3. Verify the counter maintains its value (data persistence)

**Validation Checklist:**
- [ ] Both web and redis containers start successfully
- [ ] Application accessible on http://localhost:5001
- [ ] Counter increments with each page refresh
- [ ] Data persists after container restart
- [ ] Multiple web containers can be scaled
- [ ] Services can communicate over the custom network

---

### Lab 3: Docker Swarm Multi-Node Deployment (`swarm`)

**Objective:** Deploy a scalable Flask application using Docker Swarm mode

**What you'll build:** A Flask counter app that shows which container instance serves each request

**Technologies:** Python, Flask, Redis, Docker Swarm, Container Orchestration

- Pre-built image: [Docker Hub](https://hub.docker.com/repository/docker/rangasam/gsd/) **tag:** `compose2023`

#### Lab 3 Tasks:

**Task 3.1: Initialize Docker Swarm**
1. Navigate to the `swarm` directory
2. Initialize Swarm mode (examples and common pitfalls):

   ```bash
   # -- Common typo: 'adrvertise' (misspelling) -> will produce unknown flag
   ubuntu@node1:~$ docker swarm init --adrvertise-addr 10.211.55.15
   unknown flag: --adrvertise-addr

   Usage:  docker swarm init [OPTIONS]

   Run 'docker swarm init --help' for more information

   # -- Permission denied if your user is not permitted to access the Docker socket
   ubuntu@node1:~$ docker container ls
   permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get "http://%2Fvar%2Frun%2Fdocker.sock/v1.51/containers/json": dial unix /var/run/docker.sock: connect: permission denied

   # Use sudo (or add your user to the docker group) to perform swarm admin actions
   ubuntu@node1:~$ sudo docker container ls
   CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

   # -- Another typo example: 'farm' is not a docker command
   ubuntu@node1:~$ sudo docker farm init --advertise-addr 10.211.55.15
   unknown flag: --advertise-addr

   ubuntu@node1:~$ sudo docker farm init
   docker: unknown command: docker farm

   # -- Correct command: initialize swarm with advertise address
   ubuntu@node1:~$ docker --version
   Docker version 28.4.0, build d8eb465

   # If you see a permission error, run with sudo or add user to docker group:
   #   sudo usermod -aG docker $USER && newgrp docker

   ubuntu@node1:~$ sudo docker swarm init --advertise-addr 10.211.55.15
   Swarm initialized: current node (tbvdwy58oj1ii09g5o8trlnbc) is now a manager.

   To add a worker to this swarm, run the following command (example token):

      docker swarm join --token SWMTKN-1-0g2qbsep9spedwsxqllneimqhchzgdy99ipiznqio4cuh4xu23-4hgzddvexyntd1ruyidihj487 10.211.55.15:2377

   To add a manager to this swarm, run:

      docker swarm join-token manager

   ```
3. Verify Swarm status:
   ```bash
   # On manager node (may require sudo)
   docker node ls
   ```
4. Review `compose.yml` and note the `deploy` section for replica configuration

**Task 3.2: Deploy as Docker Stack**
1. Deploy the application stack:
   ```bash
   docker stack deploy -c compose.yml counter-stack
   ```
2. List running stacks:
   ```bash
   docker stack ls
   ```
3. List services in the stack:
   ```bash
   docker stack services counter-stack
   ```

**Task 3.3: Observe Service Scaling**
1. Check running containers (should see 10 web replicas):
   ```bash
   docker service ps counter-stack_web-fe
   ```
2. Access the application and observe hostname changes:
   ```bash
   curl http://localhost:5001
   # Repeat multiple times to see different container hostnames
   ```
3. Monitor service status:
   ```bash
   docker service ls
   ```

**Task 3.4: Scale Services Dynamically**
1. Scale the web service up:
   ```bash
   docker service scale counter-stack_web-fe=15
   ```
2. Watch the scaling process:
   ```bash
   docker service ps counter-stack_web-fe
   ```
3. Scale down:
   ```bash
   docker service scale counter-stack_web-fe=5
   ```

## Examples: Scaling services (real session)

The following is a real terminal session showing common permission errors, use of `sudo`, successful scaling of services, verification with `docker service ps`, and service/container cleanup. This is included as a reference for what to expect when operating on a Swarm manager node.

```bash
ubuntu@node1:~$ docker service scale web3=5
web3: permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get "http://%2Fvar%2Frun%2Fdocker.sock/v1.51/services/web3?insertDefaults=false": dial unix /var/run/docker.sock: connect: permission denied

# Use sudo on systems where your user does not have access to the Docker socket
ubuntu@node1:~$ sudo docker service scale web3=5
web3 scaled to 5
overall progress: 5 out of 5 tasks 
1/5: running   [==================================================>] 
2/5: running   [==================================================>] 
3/5: running   [==================================================>] 
4/5: running   [==================================================>] 
5/5: running   [==================================================>] 
verify: Service web3 converged

ubuntu@node1:~$ sudo docker service scale web1=5
web1 scaled to 5
overall progress: 5 out of 5 tasks 
1/5: running   [==================================================>] 
2/5: running   [==================================================>] 
3/5: running   [==================================================>] 
4/5: running   [==================================================>] 
5/5: running   [==================================================>] 
verify: Service web1 converged

# Inspect the tasks for the service (use sudo if required on your host)
ubuntu@node1:~$ sudo docker service ps web3
ID             NAME      IMAGE                      NODE      DESIRED STATE   CURRENT STATE                ERROR     PORTS
zqzdnq15atg2   web3.1    nigelpoulton/gsd:web2023   node3     Running         Running 13 minutes ago                 
l2uaavb9n87   web3.2    nigelpoulton/gsd:web2023   node4     Running         Running 13 minutes ago                 
nl5unaj9ht87   web3.3    nigelpoulton/gsd:web2023   node5     Running         Running 13 minutes ago                 
tidf3i510aiv   web3.4    nigelpoulton/gsd:web2023   node3     Running         Running about a minute ago             
u1vbtastrlbb   web3.5    nigelpoulton/gsd:web2023   node4     Running         Running about a minute ago             

ubuntu@node1:~$ sudo docker service ps web1
ID             NAME      IMAGE                  NODE      DESIRED STATE   CURRENT STATE                ERROR     PORTS
l4wkkjc1dign   web1.1    rangasam/gsd:ctr2023   node3     Running         Running 16 minutes ago                 
ve10uo92ccmm   web1.2    rangasam/gsd:ctr2023   node4     Running         Running 16 minutes ago                 
h8hc5ck8ezvt   web1.3    rangasam/gsd:ctr2023   node5     Running         Running 16 minutes ago                 
lti7ycgksnvm   web1.4    rangasam/gsd:ctr2023   node4     Running         Running about a minute ago             
1v1gkpiaeqdf   web1.5    rangasam/gsd:ctr2023   node5     Running         Running about a minute ago             

# If non-root users see socket permission errors, either use sudo or add your user to the docker group:
#   sudo usermod -aG docker $USER
# then log out/in or restart the shell session.

# View local containers (requires permission)
ubuntu@node1:~$ docker container ls
permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get "http://%2Fvar%2Frun%2Fdocker.sock/v1.51/containers/json": dial unix /var/run/docker.sock: connect: permission denied

ubuntu@node1:~$ sudo docker container ls
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

# On worker nodes you can inspect running containers and, if necessary, stop/remove them.
ubuntu@node5:~$ sudo docker container ls
CONTAINER ID   IMAGE                      COMMAND         CREATED          STATUS          PORTS                                                             NAMES
1a2ce88eda7a   rangasam/gsd:ctr2023       "node app.js"   3 minutes ago    Up 3 minutes                                                                      web1.5.1v1gkpiaeqdffvxhnte21zd58
b91515f95a50   nigelpoulton/gsd:web2023   "node app.js"   16 minutes ago   Up 16 minutes                                                                     web3.3.nl5unaj9ht87ap8vk0ct6nbrn
3663c7060341   rangasam/gsd:ctr2023       "node app.js"   18 minutes ago   Up 18 minutes                                                                     web1.3.h8hc5ck8ezvtt9253te0pgbpy

# Stop and remove containers (use -f to force remove running containers)
ubuntu@node5:~$ sudo docker container rm 1a2ce88eda7a b91515f95a50 3663c7060341 -f
1a2ce88eda7a
b91515f95a50
3663c7060341

``` 

### Notes & troubleshooting

- Common typos/invalid commands seen in practice: `--adrvertise-addr` (typo), `docker farm` (invalid command). Use `docker swarm init --advertise-addr <IP>`.
- If `docker service ps` shows `Rejected` with `No such image`, ensure the image exists on the node or is pullable from a registry (check image name spelling).
- When removing services, use `sudo docker service rm <name>` on systems requiring root.
- To avoid repeatedly using `sudo`, add your user to the `docker` group as shown above (requires logout/login).


**Task 3.5: Update and Rolling Deployment**
1. Update the service with a rolling update:
   ```bash
   docker service update --image rangasam/gsd:swarm2023 counter-stack_web-fe
   ```
2. Monitor the update process:
   ```bash
   docker service ps counter-stack_web-fe
   ```

**Task 3.6: Cleanup**
1. Remove the stack:
   ```bash
   docker stack rm counter-stack
   ```
2. Leave Swarm mode (if desired):
   ```bash
   docker swarm leave --force
   ```

**Validation Checklist:**
- [ ] Swarm mode initializes successfully
- [ ] Stack deploys with 10 web replicas
- [ ] Application shows different hostnames on refresh
- [ ] Services can be scaled up and down dynamically
- [ ] Rolling updates complete successfully
- [ ] Counter data persists across container restarts

---

### Lab 4: Production Swarm Stack (`swarm-stack`)

**Objective:** Deploy a production-ready multi-container stack with advanced Swarm features

**What you'll build:** Enhanced Flask application with optimized Swarm configuration

**Technologies:** Python, Flask, Redis, Docker Swarm, Production Configuration

- Docker hub image: [rangasam/gsd:swarm-stack](https://hub.docker.com/repository/docker/rangasam/gsd)

#### Lab 4 Tasks:

**Task 4.1: Production Configuration Analysis**
1. Navigate to the `swarm-stack` directory
2. Compare `docker-compose.yml` with previous labs
3. Note production considerations:
   - Resource constraints
   - Restart policies
   - Health checks
   - Security configurations

**Task 4.2: Deploy Production Stack**
1. Ensure Swarm mode is active:
   ```bash
   docker swarm init --advertise-addr $(hostname -I | awk '{print $1}')
   ```
2. Deploy the production stack:
   ```bash
   docker stack deploy -c docker-compose.yml prod-counter
   ```
3. Verify deployment:
   ```bash
   docker stack services prod-counter
   ```

**Task 4.3: Production Testing**
1. Test application resilience:
   ```bash
   # Generate load
   for i in {1..100}; do curl -s http://localhost:5000 | grep -o "Container ID: [^<]*"; done
   ```
2. Simulate container failure:
   ```bash
   # Kill a container and watch Swarm recover
   docker kill $(docker ps -q --filter "label=com.docker.swarm.service.name=prod-counter_web-fe" | head -1)
   docker service ps prod-counter_web-fe
   ```

**Task 4.4: Monitor and Troubleshoot**
1. Check service health:
   ```bash
   docker service inspect prod-counter_web-fe --pretty
   ```
2. View logs across all replicas:
   ```bash
   docker service logs prod-counter_web-fe
   ```
3. Monitor resource usage:
   ```bash
   docker stats
   ```

**Validation Checklist:**
- [ ] Production stack deploys successfully
- [ ] Application handles load distribution
- [ ] Failed containers are automatically replaced
- [ ] Service discovery works between containers
- [ ] Logs and monitoring provide visibility

---

## 🔧 Development Environment Setup

### Prerequisites

- **Docker Desktop** (Windows/macOS) or **Docker Engine** (Linux)
- **Git** (command line tool)
- **VS Code** (recommended editor with built-in Git support)
- **Terminal/Command Line** access

### Recommended VS Code Extensions

```
- Docker (Microsoft) - For Docker file support and container management
- Git Graph (optional) - Visual git history
- GitLens (optional) - Enhanced git integration
```

### Installation Verification

```bash
# Verify Docker installation
docker --version
docker-compose --version

# Verify Git installation
git --version

# Test Docker functionality
docker run hello-world

# Test Git configuration
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

### Simple Git Setup (No Complex Tools Needed)

This project is designed to work with **basic Git commands only**. You don't need:
- ❌ GitKraken or other GUI Git tools
- ❌ Complex branching strategies
- ❌ Advanced Git workflows

**What you DO need:**
- ✅ Basic `git add`, `git commit`, `git push` commands
- ✅ VS Code's built-in Git integration (Source Control panel)
- ✅ Simple command line Git usage

#### Using VS Code's Built-in Git Features

VS Code provides excellent Git integration without needing external tools:

**Source Control Panel (Ctrl/Cmd + Shift + G):**
- View changed files
- Stage changes by clicking the "+" icon
- Enter commit message and press Ctrl/Cmd + Enter to commit
- Click "..." menu for push/pull options

**Command Palette (Ctrl/Cmd + Shift + P):**
- Type "Git: " to see all Git commands
- Use "Git: Push" to push changes
- Use "Git: Pull" to get updates

**Terminal Integration:**
- Use VS Code's integrated terminal (Ctrl/Cmd + `)
- Run basic git commands directly: `git status`, `git add .`, `git commit -m "message"`, `git push`

### Common Commands Reference

```bash
# Container Management
docker build -t <tag> .                 # Build image
docker run -d -p <host>:<container> <image>  # Run container
docker ps                               # List running containers
docker stop <container>                 # Stop container
docker rm <container>                   # Remove container

# Image Management
docker images                           # List images
docker rmi <image>                      # Remove image
docker pull <image>                     # Pull from registry

# Compose Commands
docker-compose up -d                    # Start services
docker-compose down                     # Stop services
docker-compose logs <service>           # View logs
docker-compose exec <service> <command> # Execute in service

# Swarm Commands
docker swarm init                       # Initialize swarm
docker stack deploy -c <file> <name>    # Deploy stack
docker service ls                       # List services
docker service scale <service>=<count>  # Scale service
```

### Git Commands for Repository Management

```bash
# Essential Git Commands (Basic Workflow)

# 1. Check what files have changed
git status

# 2. Add files to staging area
git add .                              # Add all changed files
git add filename.txt                   # Add specific file

# 3. Save changes with a message
git commit -m "Describe your changes here"

# 4. Send changes to GitHub
git push

# 5. Get latest changes from GitHub
git pull
```

#### Simple Daily Git Workflow

```bash
# Standard workflow for updating this project:

# Step 1: Check current status
git status

# Step 2: Add your changes
git add .

# Step 3: Commit with a clear message
git commit -m "Update lab documentation"

# Step 4: Push to GitHub
git push

# That's it! Your changes are now on GitHub.
```

#### Useful Git Tips

```bash
# View recent commits
git log --oneline -5                   # Show last 5 commits

# Undo last commit (keeps changes)
git reset --soft HEAD~1

# See what changed in a file
git diff filename.txt

# Quick add and commit in one line
git commit -am "Quick update"          # Only works for modified files

# Check remote repository URL
git remote -v
```

#### Common Git Scenarios for This Project

```bash
# Scenario 1: Adding a new lab exercise
git add .
git commit -m "Add Lab 5: Docker networking exercise"
git push

# Scenario 2: Fixing documentation
git add README.md
git commit -m "Fix typo in Lab 2 instructions"
git push

# Scenario 3: Updating Docker configurations
git add compose/
git commit -m "Update compose file for Redis version"
git push

# Scenario 4: Working collaboratively
git pull                               # Get latest changes first
# Make your changes...
git add .
git commit -m "Your changes"
git push
```

---

## 📚 Additional Resources

- [Docker Official Documentation](https://docs.docker.com/)
- [Docker Compose Reference](https://docs.docker.com/compose/)
- [Docker Swarm Tutorial](https://docs.docker.com/engine/swarm/)
- [Best Practices for Dockerfile](https://docs.docker.com/develop/dev-best-practices/)

---

## 🤝 Support and Troubleshooting

### Common Issues

1. **Port conflicts**: Ensure ports 5000, 5001, 8080 are not in use
2. **Permission errors**: Run Docker commands with appropriate privileges
3. **Network issues**: Check Docker network configuration
4. **Image build failures**: Verify Dockerfile syntax and dependencies

### Getting Help

**Docker Issues:**
- Check container logs: `docker logs <container>`
- Verify service status: `docker service ps <service>`
- Review compose configuration: `docker-compose config`

**Git Issues:**
- Check repository status: `git status`
- See what changed: `git diff`
- View recent commits: `git log --oneline -5`
- Undo last commit (keep changes): `git reset --soft HEAD~1`
- Get help: `git help <command>` (e.g., `git help push`)

**VS Code Git Issues:**
- Refresh Source Control panel: Click refresh icon
- Use Command Palette: Ctrl/Cmd + Shift + P, then type "Git: "
- Check Output panel: View > Output > Select "Git" from dropdown

---

## 📝 Notes Section

Use this space for your own notes during the labs:

```
Lab 1 Notes:
- 

Lab 2 Notes:
- 

Lab 3 Notes:
- 

Lab 4 Notes:
- 
```

---

*Happy containerizing! 🐳*
