# Docker Container Network Isolation

## Problem Statement

We need to set up a Docker-based system where:
1. There are two containers:  
   - **Container 1** hosts a service (e.g., Nginx).  
   - **Container 2** acts as a client that accesses the service in Container 1.  
2. **Container 1** must remain isolated and inaccessible from the host or any other container outside its network.  
3. Only **Container 2** can communicate with **Container 1**.

---

## Objective

- Implement secure inter-container communication using Docker networking.
- Isolate **Container 1** to prevent unauthorized access.
- Explore Docker's networking model and how containers communicate.

---

## Networking Concepts

### 1. **Docker Networks**
Docker uses networks to enable containers to communicate securely. By default, Docker provides several network types:
- **Bridge (default):** A private network created for communication between containers.
- **Host:** Shares the host’s networking stack (not used here for isolation).
- **None:** No network interface (used for strict isolation).
- **Custom Bridge:** A user-defined bridge for more control over container communication (used in this project).

### 2. **Custom Bridge Network**
A custom bridge network is:
- Isolated from the default bridge (`bridge`).
- Enables containers to discover and communicate using container names as DNS.
- Provides controlled access between containers.

### 3. **DNS in Docker Networks**
In a custom network:
- Docker automatically creates an internal DNS server.
- Containers can resolve each other’s names (e.g., `http://container1`) without requiring IP addresses.

### 4. **Container Port Exposure**
- Ports can be exposed to the host using the `-p` or `-P` options.  
- In this project, **Container 1** does not expose ports to maintain isolation.

---

## Architecture Overview

The setup involves:
- A **custom bridge network** named `my_private_network`.
- **Container 1** runs an Nginx server, accessible only within the network.
- **Container 2** is a client container that interacts with **Container 1** using its DNS name.

**Diagram**  
```plaintext
+-------------------+       +-------------------+
|    Container 1    |       |    Container 2    |
|  (Nginx Server)   |       |   (Client Tool)   |
|-------------------|       |-------------------|
| DNS: container1   | <---> | DNS: container2   |
| Private Network   |       | Private Network   |
+-------------------+       +-------------------+
         ^
         | Docker Custom Network
         |
    +----------------+
    | my_private_network |
    +----------------+
```

---

## Steps to Implement

### 1. Create a Private Docker Network
```bash
docker network create my_private_network
```

### 2. Run Container 1 (Isolated Service)
```bash
docker run --name container1 --network my_private_network -d nginx
```

### 3. Run Container 2 (Client)
```bash
docker run --name container2 --network my_private_network -it busybox
```

### 4. Test Connectivity
1. Open a shell in **Container 2**:
   ```bash
   docker exec -it container2 sh
   ```
2. Access the service running in **Container 1**:
   ```bash
   wget -qO- http://container1
   ```

### 5. Verify Isolation
1. Get the IP of **Container 1**:
   ```bash
   docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' container1
   ```
2. Try accessing it from the host machine:
   ```bash
   curl http://<container1-IP>
   ```
   This should fail, confirming **Container 1** is isolated.

---

## Behind the Scenes: Networking in Action

### 1. **Network Creation**
When you create `my_private_network`, Docker sets up:
- A new Linux bridge interface (e.g., `br-xxxxx`).
- Rules for routing traffic between containers attached to the bridge.

### 2. **Container Communication**
- Docker assigns unique IPs to containers in the private network.  
- The custom bridge allows containers to resolve each other by name using Docker’s internal DNS.

### 3. **Traffic Isolation**
Containers in `my_private_network` are isolated from the default `bridge` network. Traffic stays within the custom network, ensuring no external access.

### 4. **No Host Exposure**
- **Container 1** doesn’t expose ports (`-p` or `-P` not used), ensuring it cannot be accessed from the host or external networks.  
- Only containers in `my_private_network` can reach **Container 1**.

---

## Advanced Features

### 1. Using Docker Compose
Simplify the setup with a `docker-compose.yml` file:
```yaml
version: '3.8'
services:
  container1:
    image: nginx
    networks:
      - my_private_network

  container2:
    image: busybox
    command: ["sh"]
    networks:
      - my_private_network

networks:
  my_private_network:
    driver: bridge
```

Run the setup with:
```bash
docker-compose up -d
```

### 2. Adding Firewall Rules
Enhance security by adding custom `iptables` rules for the bridge network.

### 3. Scaling
Scale the client service (`container2`) to multiple instances:
```bash
docker-compose up --scale container2=3 -d
```

---

## Expected Results

1. **Connectivity:**  
   - **Container 2** can access **Container 1** using its DNS name (`container1`).
2. **Isolation:**  
   - **Container 1** is inaccessible from the host or other containers not attached to `my_private_network`.

---

## Use Cases

- **Microservices Security:** Limit access to internal services (e.g., databases, APIs).  
- **Development Environments:** Create isolated environments for testing without exposing internal services.  
- **Compliance:** Enforce network segmentation to meet security and compliance standards.

---

## Troubleshooting

### Issue: "Service not reachable"
- Ensure both containers are on the same network:
  ```bash
  docker network inspect my_private_network
  ```

### Issue: "Cannot resolve container name"
- Restart the containers to refresh DNS records:
  ```bash
  docker restart container1 container2
  ```

---

## Additional Resources

- [Docker Networking Documentation](https://docs.docker.com/network/)
- [Understanding Bridge Networks](https://docs.docker.com/network/bridge/)
- [Docker Compose Networking](https://docs.docker.com/compose/networking/)
