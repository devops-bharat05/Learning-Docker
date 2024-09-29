# Docker Study Plan: Beginner to Advanced

## Day 1: Introduction to Containers and Docker

### What are Containers?
Containers are lightweight, stand-alone, and executable software packages that include everything needed to run a piece of software—code, runtime, libraries, and system tools. Unlike traditional virtual machines, containers share the host system’s kernel, making them more efficient in terms of resource usage.

#### Key Benefits of Containers:
- **Isolation:** Containers encapsulate applications in a way that ensures there are no conflicts between dependencies and configurations.
- **Portability:** Once a container is created, it can be run anywhere without modification—on a developer's laptop, on-premise data centers, or in the cloud.
- **Efficiency:** Containers use fewer resources than VMs because they don’t require a full operating system (OS) for each instance.

### History of Containers vs. Virtual Machines (VMs)
- **Virtual Machines (VMs):**
  - VMs emulate entire physical machines, including their operating systems (OS), which consume a lot of resources.
  - Hypervisors, such as VMware or Hyper-V, manage multiple VMs on a single host.
  
- **Containers:**
  - Containers emerged as a lightweight alternative to VMs.
  - Instead of virtualizing hardware, containers virtualize the operating system and run multiple workloads on the same OS kernel.
  
  | Feature                 | Virtual Machines               | Containers                   |
  |-------------------------|--------------------------------|------------------------------|
  | Isolation                | Full OS-level isolation        | Process-level isolation       |
  | Boot Time                | Minutes                        | Seconds                       |
  | Resource Overhead        | High                           | Low                           |
  | Performance              | Slightly slower                | Near-native                   |

### Introduction to Docker
Docker is an open-source platform designed to automate the deployment, scaling, and management of applications in containers. Docker allows you to package an application with its dependencies and run it as a container across multiple environments, from local development to cloud deployments.

#### Docker Key Features:
- **Image-Based Deployment:** Docker uses images to build containers. Images are lightweight and reusable.
- **Efficient Version Control:** Docker allows developers to version-control their images, similar to how Git manages code versions.
- **Networking:** Docker simplifies networking by providing built-in options for container communication.
- **Scaling & Orchestration:** Docker containers can easily be scaled across multiple servers using orchestration tools like Kubernetes and Docker Swarm.

### Installing Docker on Different Platforms

#### 1. Installing Docker on Windows
- **Prerequisites:** Windows 10 (Pro, Enterprise, Education) or Windows Server.
- **Steps:**
  1. Download Docker Desktop from the [official Docker website](https://www.docker.com/products/docker-desktop).
  2. Run the installer and follow the setup instructions.
  3. After installation, open PowerShell or Command Prompt to verify installation:
     ```bash
     docker --version
     ```

#### 2. Installing Docker on macOS
- **Prerequisites:** macOS 10.14 (Mojave) or later.
- **Steps:**
  1. Download Docker Desktop for Mac from [Docker’s website](https://www.docker.com/products/docker-desktop).
  2. Double-click the `.dmg` file and drag the Docker icon into your Applications folder.
  3. Launch Docker Desktop.
  4. Verify the installation via the terminal:
     ```bash
     docker --version
     ```

#### 3. Installing Docker on Linux (Ubuntu Example)
- **Prerequisites:** A system running Ubuntu 18.04 or later.
- **Steps:**
  1. Update your existing packages:
     ```bash
     sudo apt-get update
     ```
  2. Install Docker’s dependencies:
     ```bash
     sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
     ```
  3. Add Docker’s official GPG key:
     ```bash
     curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
     ```
  4. Add the Docker repository to APT sources:
     ```bash
     sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
     ```
  5. Install Docker:
     ```bash
     sudo apt-get update
     sudo apt-get install docker-ce
     ```
  6. Verify Docker installation:
     ```bash
     docker --version
     ```

#### Post-installation Steps for Linux
- **Manage Docker as a non-root user:**
  1. Create a Docker group:
     ```bash
     sudo groupadd docker
     ```
  2. Add your user to the Docker group:
     ```bash
     sudo usermod -aG docker $USER
     ```
  3. Log out and log back in to apply the changes.

### Verifying Docker Installation
After installing Docker, verify everything is set up correctly by running a test container:
```bash
docker run hello-world
```

This command pulls a Docker image, runs a container, and outputs a confirmation message.

---

## Next Steps: Beginner to Advanced Topics

This introduction provides a foundation for understanding Docker and setting up the development environment. The next steps involve diving deeper into Docker concepts such as building Docker images, managing containers, working with Docker Compose, and orchestration with Kubernetes.

### Resources:
- [Docker Official Documentation](https://docs.docker.com/)
- [DockerHub - Public Container Images](https://hub.docker.com/)
- [Play with Docker (Online Lab Environment)](https://labs.play-with-docker.com/)

By the end of this learning journey, you will be proficient in Docker, capable of developing and deploying containerized applications, and prepared to work with advanced orchestration tools like Kubernetes.
