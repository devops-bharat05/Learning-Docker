### **NGINX Detailed README**

# NGINX

## **Table of Contents**

1. [Introduction to NGINX](#introduction-to-nginx)
2. [Why Do We Need NGINX?](#why-do-we-need-nginx)
3. [NGINX: Process-Driven vs. Event-Driven](#nginx-process-driven-vs-event-driven)
4. [Roles of NGINX](#roles-of-nginx)
    - Reverse Proxy
    - Load Balancer
    - Web Server
    - Caching Server
    - API Gateway
    - Security Enhancements
5. [Various Configurations](#various-configurations)
    - Basic Configuration Structure
    - Reverse Proxy Configuration
    - Load Balancing Configuration
    - SSL/TLS Configuration
    - Caching Configuration
    - Gzip Compression Configuration
    - Logging Configuration
6. [Conclusion](#conclusion)

---

## **Introduction to NGINX**

**NGINX** is a high-performance, open-source web server that also acts as a reverse proxy, load balancer, and HTTP cache. It is widely used due to its ability to handle large numbers of simultaneous connections with minimal resources. Created in 2004 by Igor Sysoev, NGINX's efficient architecture has made it one of the most popular web servers globally, with capabilities that extend beyond typical web server use cases.

---

## **Why Do We Need NGINX?**

NGINX is an essential tool for modern web infrastructure. Some key reasons why NGINX is used:

1. **Handling High Traffic Loads**: NGINX can efficiently handle thousands of simultaneous requests, making it perfect for high-traffic websites.
2. **Reverse Proxy**: It forwards client requests to one or more backend servers and serves as an intermediary for requests between clients and backend applications.
3. **Load Balancing**: NGINX can distribute client requests across multiple servers to balance traffic and ensure high availability.
4. **Serving Static Content**: It can efficiently serve static files like HTML, CSS, and JavaScript, reducing the load on backend servers.
5. **SSL/TLS Termination**: NGINX manages secure communication via SSL/TLS, offloading encryption and decryption from application servers.
6. **Caching**: It can cache content to improve response times and reduce load on backend servers.
7. **Security**: NGINX can block malicious requests, prevent DDoS attacks, and implement security policies.

---

## **NGINX: Process-Driven vs. Event-Driven**

**Event-Driven**: NGINX is built on an event-driven architecture. Unlike process-driven servers, which create a new thread or process for each incoming request, NGINX uses an event-driven model with asynchronous, non-blocking I/O to handle multiple requests concurrently.

### **Why Event-Driven?**
- **Scalability**: NGINX's event-driven nature allows it to handle many connections simultaneously using fewer resources.
- **Efficiency**: Asynchronous, non-blocking operations make it more efficient in managing server resources like CPU and memory.
- **Low Latency**: It offers lower latency, improving performance and speed when handling numerous clients.

NGINX can process many requests in parallel within a single worker process, minimizing overhead and maximizing performance.

---

## **Roles of NGINX**

NGINX plays multiple roles in modern web architecture. Here are some of its primary roles:

### **1. Reverse Proxy**
NGINX acts as a **reverse proxy** by forwarding requests from clients to one or more backend servers. This allows backend applications to remain hidden from direct exposure to the internet and centralizes request routing, logging, and monitoring.

**Example:**
```nginx
server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass http://backend_server;
    }
}
```

### **2. Load Balancer**
NGINX can distribute client requests across multiple servers to spread the load evenly and ensure system reliability. It supports various load-balancing algorithms like round-robin, least connections, and IP hashing.

**Example:**
```nginx
upstream backend {
    server backend1.example.com;
    server backend2.example.com;
}

server {
    location / {
        proxy_pass http://backend;
    }
}
```

### **3. Web Server**
NGINX can also function as a traditional **web server** to serve static files like HTML, CSS, JavaScript, images, and videos.

**Example:**
```nginx
server {
    listen 80;
    server_name example.com;

    location / {
        root /var/www/html;
        index index.html;
    }
}
```

### **4. Caching Server**
NGINX can act as a **cache** to store copies of frequently requested content. This reduces load on backend servers and speeds up content delivery.

**Example:**
```nginx
location / {
    proxy_cache my_cache;
    proxy_pass http://backend_server;
}
```

### **5. API Gateway**
NGINX can serve as an **API Gateway**, managing and routing API traffic between microservices and external clients, often handling tasks such as authentication, rate limiting, and request/response transformation.

### **6. Security Enhancements**
NGINX can also be used to enhance security through rate limiting, filtering bad requests, blocking malicious traffic, and handling SSL/TLS certificates.

---

## **Various Configurations**

### **Basic Configuration Structure**
NGINX configuration files are typically located in `/etc/nginx/nginx.conf` or `/etc/nginx/sites-available`. The file is divided into context blocks such as `events`, `http`, and `server`.

**Example: Basic NGINX Configuration**
```nginx
user nginx;
worker_processes 4;

events {
    worker_connections 1024;
}

http {
    server {
        listen 80;
        server_name example.com;

        location / {
            root /var/www/html;
            index index.html;
        }
    }
}
```

### **1. Reverse Proxy Configuration**
NGINX can forward requests to backend servers using the `proxy_pass` directive.

**Example:**
```nginx
server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass http://backend_server;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### **2. Load Balancing Configuration**
NGINX can load balance traffic between multiple backend servers.

**Example:**
```nginx
upstream backend_servers {
    server backend1.example.com;
    server backend2.example.com;
}

server {
    listen 80;
    location / {
        proxy_pass http://backend_servers;
    }
}
```

### **3. SSL/TLS Configuration**
NGINX can handle SSL termination, which decrypts requests before sending them to the backend servers.

**Example:**
```nginx
server {
    listen 443 ssl;
    server_name example.com;

    ssl_certificate /etc/nginx/ssl/example.com.crt;
    ssl_certificate_key /etc/nginx/ssl/example.com.key;

    location / {
        proxy_pass http://backend_server;
    }
}
```

### **4. Caching Configuration**
Caching static assets and content can significantly reduce backend load and improve response times.

**Example:**
```nginx
proxy_cache_path /data/nginx/cache levels=1:2 keys_zone=my_cache:10m max_size=10g;

server {
    location / {
        proxy_cache my_cache;
        proxy_pass http://backend_server;
    }
}
```

### **5. Gzip Compression Configuration**
Enable gzip compression to reduce bandwidth usage by compressing files before sending them to clients.

**Example:**
```nginx
http {
    gzip on;
    gzip_types text/css application/javascript image/svg+xml;
    gzip_proxied any;
}
```

### **6. Logging Configuration**
NGINX can log access and errors to files or send logs to a centralized system.

**Example:**
```nginx
http {
    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" "$http_x_forwarded_for"';

    access_log /var/log/nginx/access.log main;
    error_log /var/log/nginx/error.log warn;
}
```
Certainly! Let's dive deeper into the advanced aspects of **NGINX** and how it can be configured for performance, security, and scalability.

---

### **7. NGINX Performance Tuning**
Performance tuning is essential to optimizing NGINX for handling large-scale traffic and ensuring low-latency responses. Various factors like worker processes, buffers, and timeouts can be tweaked to enhance performance.

#### **7.1 Worker Processes**
The `worker_processes` directive determines the number of worker processes that handle requests. Generally, it is recommended to match the number of worker processes to the number of CPU cores on the server.

**Example:**
```nginx
worker_processes auto;
```
This setting will automatically set the number of worker processes based on the available CPU cores.

#### **7.2 Worker Connections**
The `worker_connections` directive defines how many concurrent connections each worker process can handle. The total number of simultaneous connections that NGINX can handle is `worker_processes` multiplied by `worker_connections`.

**Example:**
```nginx
events {
    worker_connections 1024;
}
```
In this example, if you have 4 worker processes, NGINX can handle 4 x 1024 = 4096 concurrent connections.

#### **7.3 Buffers and Timeouts**
Tuning buffers and timeouts can help improve performance by managing client-server interaction efficiently.

- **Client Body Buffer**: Defines the maximum size of the buffer used for client request bodies.
  
  **Example:**
  ```nginx
  client_body_buffer_size 16K;
  ```

- **Client Header Buffer**: Defines the size of the buffer for client headers.
  
  **Example:**
  ```nginx
  client_header_buffer_size 1k;
  ```

- **Keepalive Timeout**: Sets the timeout for keeping a client connection open.
  
  **Example:**
  ```nginx
  keepalive_timeout 65;
  ```

---

### **8. NGINX Security Configurations**

Security is a critical consideration when using NGINX as it sits between the external world and your internal services. Here are some key security-related configurations.

#### **8.1 Enabling HTTP Security Headers**
Security headers help protect your web applications by adding layers of defense against attacks like XSS and clickjacking.

**Example:**
```nginx
server {
    add_header X-Content-Type-Options nosniff;
    add_header X-Frame-Options DENY;
    add_header X-XSS-Protection "1; mode=block";
}
```

#### **8.2 Limiting Request Rate (Rate Limiting)**
Rate limiting is used to prevent denial-of-service (DoS) attacks by limiting the number of requests a client can make to the server in a specific timeframe.

**Example:**
```nginx
http {
    limit_req_zone $binary_remote_addr zone=mylimit:10m rate=10r/s;

    server {
        location / {
            limit_req zone=mylimit burst=20 nodelay;
        }
    }
}
```
This configuration limits requests to 10 per second, with a burst limit of 20.

#### **8.3 Denying Malicious User Agents**
NGINX can filter requests based on user agents to block malicious or unwanted traffic.

**Example:**
```nginx
server {
    if ($http_user_agent ~* (crawler|bot) ) {
        return 403;
    }
}
```

#### **8.4 Blocking IP Addresses**
You can block specific IP addresses or ranges to prevent them from accessing your server.

**Example:**
```nginx
server {
    deny 192.168.1.1;
    allow all;
}
```

#### **8.5 Enforcing SSL/TLS with HSTS**
HTTP Strict Transport Security (HSTS) forces clients to interact with the server only over HTTPS, ensuring secure connections.

**Example:**
```nginx
server {
    listen 443 ssl;
    server_name example.com;

    ssl_certificate /etc/nginx/ssl/example.com.crt;
    ssl_certificate_key /etc/nginx/ssl/example.com.key;

    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
}
```

#### **8.6 Protecting Backend Servers**
When NGINX operates as a reverse proxy, you should configure it to block access to sensitive paths such as `/admin` or `/login`.

**Example:**
```nginx
location /admin {
    deny all;
}
```

---

### **9. NGINX Load Balancing Algorithms**

NGINX supports various load-balancing algorithms to distribute traffic among multiple servers.

#### **9.1 Round Robin**
The simplest load-balancing method where each request is distributed to the next server in line.

**Example:**
```nginx
upstream backend {
    server backend1.example.com;
    server backend2.example.com;
}

server {
    location / {
        proxy_pass http://backend;
    }
}
```

#### **9.2 Least Connections**
Distributes traffic based on the number of active connections. Requests are sent to the server with the least number of active connections.

**Example:**
```nginx
upstream backend {
    least_conn;
    server backend1.example.com;
    server backend2.example.com;
}
```

#### **9.3 IP Hash**
Distributes traffic based on the client’s IP address, ensuring that requests from the same IP address are always sent to the same server.

**Example:**
```nginx
upstream backend {
    ip_hash;
    server backend1.example.com;
    server backend2.example.com;
}
```

---

### **10. NGINX Monitoring and Logging**

Effective monitoring and logging are essential to understanding the performance and health of your NGINX server.

#### **10.1 Access Logs**
The access log captures every client request and is useful for monitoring traffic patterns.

**Example:**
```nginx
http {
    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" "$http_x_forwarded_for"';
    access_log /var/log/nginx/access.log main;
}
```

#### **10.2 Error Logs**
The error log captures issues encountered by NGINX, such as failed requests or server errors.

**Example:**
```nginx
server {
    error_log /var/log/nginx/error.log warn;
}
```

#### **10.3 Monitoring with Third-Party Tools**
You can integrate NGINX with various third-party monitoring tools, such as:

- **Prometheus & Grafana**: To collect NGINX metrics and visualize them on dashboards.
- **Datadog**: To monitor NGINX performance and logs in real-time.

To expose NGINX metrics for Prometheus:
```nginx
location /metrics {
    stub_status;
    allow 127.0.0.1;
    deny all;
}
```

---

### **11. NGINX as a Content Delivery Network (CDN)**

NGINX can be configured to act as a CDN by serving static assets closer to users and caching content.

#### **11.1 Content Caching**
NGINX’s caching mechanism can be used to store static resources such as images, CSS, and JavaScript files. Cached content improves load times for repeat requests.

**Example:**
```nginx
proxy_cache_path /var/cache/nginx levels=1:2 keys_zone=static_cache:10m;

server {
    location /static/ {
        proxy_cache static_cache;
        proxy_pass http://backend;
    }
}
```

#### **11.2 Gzip Compression**
You can use Gzip compression to reduce the size of the files sent to clients, which improves load times and reduces bandwidth usage.

**Example:**
```nginx
http {
    gzip on;
    gzip_comp_level 6;
    gzip_types text/css application/javascript image/svg+xml;
}
```

---

### **12. NGINX for Microservices and APIs**

NGINX can be used to route requests in a microservices architecture, making it an excellent choice for API Gateway functionality.

#### **12.1 API Gateway Pattern**
NGINX can serve as a central entry point for microservices, managing routing, authentication, and load balancing between services.

**Example:**
```nginx
server {
    location /api/v1/ {
        proxy_pass http://api_v1_backend;
    }

    location /api/v2/ {
        proxy_pass http://api_v2_backend;
    }
}
```

#### **12.2 Authentication and Authorization**
NGINX can handle authentication by integrating with external systems or services like OAuth2, JWT, or LDAP.

**Example:**
```nginx
location /secure/ {
    auth_basic "Restricted Content";
    auth_basic_user_file /etc/nginx/.htpasswd;
}
```

---

### **13. NGINX High Availability (HA) Setup**

For critical applications, ensuring that your NGINX service remains available is crucial. You can set up NGINX in a high-availability (HA) configuration using various strategies.

#### **13.1 Active-Passive HA Setup**
In this setup, one NGINX instance is active, while another remains on standby. If the active instance fails, the passive one takes over.

#### **13.2 Active-Active HA Setup**
In an active-active configuration, multiple NGINX instances handle traffic simultaneously. This setup is often paired with a load balancer like Keepalived or HAProxy to distribute traffic evenly across all instances.

**Example with Keepalived:**
```shell
vrrp_instance VI_1 {
    state MASTER
    interface eth0
    virtual_router_id 51


    priority 100
    advert_int 1
    virtual_ipaddress {
        192.168.1.100
    }
}
```

--- 
## **Conclusion**

NGINX is a versatile and powerful tool for modern web applications, capable of acting as a reverse proxy, load balancer, web server, and more. Its event-driven architecture makes it efficient and scalable, while its flexibility in configuration enables it to handle a variety of use cases, from serving static files to SSL termination and caching. For a more in-depth understanding of NGINX, explore the various configurations and hands-on use cases that best suit your application's needs. By optimizing for performance, security, load balancing, and scalability, NGINX becomes a powerful tool for handling everything from small websites to large-scale microservices applications.

