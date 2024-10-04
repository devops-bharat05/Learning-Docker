### **Day 2: Understanding Docker Architecture**

On **Day 2** of your Docker journey, you'll dive deep into the **Docker architecture** and understand how the different components interact. We'll break down each part of the Docker Engine, its workflow, and the differences between Docker images and containers. Hands-on labs and case study questions will solidify your understanding.

---

### **Topics to Cover**
1. **Docker Engine Overview**
2. **Docker Engine Components**: 
   - Docker Client
   - Docker Daemon
   - Docker REST API
3. **Docker Workflow**
4. **Differences Between Images and Containers**
5. **Hands-on Lab**
6. **Case Study Questions**

---

### **1. Docker Engine Overview**

**Docker Engine** is the core part of Docker. It runs as a service on your host machine and manages the creation, execution, and destruction of containers. Docker Engine consists of the following three major components:
- **Docker Client**: The CLI interface for users to interact with Docker.
- **Docker Daemon**: The background service that does the heavy lifting (building, running containers, etc.).
- **Docker REST API**: The interface through which the client and daemon communicate.

---

### **2. Docker Engine Components**

#### **Docker Client**
The Docker Client is a command-line interface (CLI) that allows users to interact with the Docker Daemon. It is where you run commands like `docker run`, `docker build`, `docker pull`, and `docker push`.

- **Commands**: The client sends high-level commands (via the REST API) to the daemon.
- **CLI example**: 
    ```bash
    docker run hello-world
    ```

#### **Docker Daemon**
The Docker Daemon is the background service that listens for requests from the Docker Client or API calls and manages containers, images, and networks.

- **Responsibilities**:
  - Creates and manages containers.
  - Pulls images from registries (like Docker Hub).
  - Handles networking and storage for containers.

#### **Docker REST API**
The Docker REST API is an HTTP API that allows developers to interact programmatically with the Docker Daemon. Docker Client commands essentially send API calls to the Docker Daemon.

- **Interaction**: Every action in Docker (e.g., running a container, building an image) is a REST API request from the Client to the Daemon.

---

### **3. Docker Workflow**

Here’s a typical workflow for using Docker:

1. **Docker Client** sends a command to run a container (`docker run`).
2. The **Docker Client** communicates with the **Docker Daemon** using the **Docker REST API**.
3. The **Docker Daemon** checks if the requested image is available locally. If not, it pulls it from Docker Hub (or another registry).
4. The **Docker Daemon** creates the container based on the image.
5. The **Docker Daemon** starts the container and keeps it running.
6. Users can then interact with the container via the CLI or expose ports for external applications to connect.

---

### **4. Differences Between Docker Images and Containers**

| **Docker Images** | **Docker Containers** |
|------------------|----------------------|
| Docker images are **read-only** templates used to create containers. | Containers are **running instances** of images. |
| Stored in Docker registries (like Docker Hub). | Created from Docker images. |
| Immutable, meaning once built, the image doesn't change. | Containers can be modified (e.g., new data added), and their state can be committed back into a new image. |
| Examples: `nginx`, `ubuntu`, `redis`. | Instances of those images running in isolation. |
| Can be shared across different environments. | Used for running applications and processes. |

---

### **5. Hands-On Lab: Docker Workflow and Architecture**

#### **Objective**:
Learn how to interact with Docker’s architecture, from pulling images to creating and running containers.

#### **Prerequisites**:
- Docker installed on your local machine.

#### **Steps**:

1. **Check Docker Installation and Daemon Status**:
   - Make sure Docker is installed:
     ```bash
     docker --version
     ```
   - Check if the Docker Daemon is running:
     ```bash
     sudo systemctl status docker
     ```

2. **Pull an Image**:
   - Pull the **nginx** image from Docker Hub:
     ```bash
     docker pull nginx
     ```

3. **Inspect the Pulled Image**:
   - View the downloaded image:
     ```bash
     docker images
     ```
   - Inspect the layers of the nginx image:
     ```bash
     docker history nginx
     ```

4. **Run a Container**:
   - Start an **nginx** container:
     ```bash
     docker run -d -p 8080:80 nginx
     ```
   - Check running containers:
     ```bash
     docker ps
     ```

5. **View Docker Logs**:
   - View logs for the **nginx** container:
     ```bash
     docker logs <container_id>
     ```

6. **Stop and Remove the Container**:
   - Stop the container:
     ```bash
     docker stop <container_id>
     ```
   - Remove the container:
     ```bash
     docker rm <container_id>
     ```

7. **Commit a New Image**:
   - Create a new container based on **ubuntu**:
     ```bash
     docker run -it ubuntu
     ```
   - Make some changes (e.g., install a package inside the container):
     ```bash
     apt update && apt install -y vim
     ```
   - Exit the container:
     ```bash
     exit
     ```
   - Commit the container changes into a new image:
     ```bash
     docker commit <container_id> my-custom-ubuntu
     ```
   - View the newly created image:
     ```bash
     docker images
     ```

---

### **6. Case Study Questions**

1. **Question 1**:  
   You have an existing Docker image and want to create multiple containers based on it. Explain how Docker Client, Docker Daemon, and Docker REST API interact during this process.

2. **Question 2**:  
   What are the differences between running an application in a Docker container versus running it directly on the host machine? Discuss resource isolation, portability, and ease of deployment.

3. **Question 3**:  
   After running several containers, you notice that some containers fail due to a missing image. How would Docker resolve this issue, and how can you avoid such problems in the future?

4. **Question 4**:  
   If you have an nginx container running on port 8080, how would you access this web server from a web browser? How does Docker handle port mapping?

5. **Question 5**:  
   How would you use Docker to ensure that a container can be restarted automatically if it crashes? What kind of configuration would you set?

---

### **Summary**
On Day 2, you explored the architecture of Docker, including how the Docker Client, Daemon, and REST API work together. You also learned the difference between images and containers and completed hands-on labs to reinforce this knowledge. The case study questions will challenge your understanding of Docker’s core architecture and operations.

