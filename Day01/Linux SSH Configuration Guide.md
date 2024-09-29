# **Linux SSH Configuration Guide**

### **Introduction to SSH**
SSH (Secure Shell) is a protocol used to securely log into remote systems over an unsecured network. It provides strong encryption, authentication, and integrity checks.

---

### **1. SSH Configuration Files**
- **`/etc/ssh/sshd_config`**: This is the server-side configuration file. It controls the behavior of the SSH daemon (`sshd`).
- **`~/.ssh/config`**: This is the client-side configuration file used to configure SSH client options.

#### **Server Configuration File (`sshd_config`)**
- Located at `/etc/ssh/sshd_config`, this file configures the SSH daemon.
- To apply changes, restart the SSH service using:  
  ```bash
  sudo systemctl restart sshd
  ```

---

### **2. Important SSH Server Configuration Options (sshd_config)**

#### **Port**  
Specifies the port number that SSH will listen on. The default is `22`.
```bash
Port 22
```

#### **PermitRootLogin**  
Controls whether the root user can log in via SSH. It's recommended to disable root login for security.
```bash
PermitRootLogin no
```

#### **PasswordAuthentication**  
Specifies if password authentication is allowed. Disabling password authentication encourages key-based authentication.
```bash
PasswordAuthentication no
```

#### **PubkeyAuthentication**  
Enables public key-based authentication.
```bash
PubkeyAuthentication yes
```

#### **AllowUsers**  
Restrict login to specific users.
```bash
AllowUsers user1 user2
```

#### **AllowGroups**  
Allows specific groups to log in.
```bash
AllowGroups sshusers
```

#### **PermitEmptyPasswords**  
Disables login to accounts with empty passwords.
```bash
PermitEmptyPasswords no
```

#### **MaxAuthTries**  
Limits the number of authentication attempts per connection.
```bash
MaxAuthTries 3
```

#### **X11Forwarding**  
Enables/disables forwarding of X11 (graphical) sessions.
```bash
X11Forwarding yes
```

#### **ClientAliveInterval**  
Sends a message to the client every N seconds to keep the connection alive.
```bash
ClientAliveInterval 60
```

#### **ClientAliveCountMax**  
Determines the number of client keepalive messages sent without receiving any response before terminating the session.
```bash
ClientAliveCountMax 3
```

---

### **3. SSH Client Configuration (`~/.ssh/config`)**

This file is used to configure per-host SSH client options.

#### **Basic Format**
```bash
Host hostname
    HostName example.com
    User your-username
    Port 22
    IdentityFile ~/.ssh/id_rsa
```

#### **Options in `~/.ssh/config`**
- **Host**: The alias for the remote host.
- **HostName**: The real domain name or IP address of the remote host.
- **User**: The default username to log in as.
- **Port**: Specifies the port to connect to on the remote host.
- **IdentityFile**: Specifies the private key file used for authentication.

---

### **4. Key-based Authentication Setup**

#### **Step 1: Generate SSH Keys**
Use the following command to generate an SSH key pair:
```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```
By default, keys are stored in `~/.ssh/id_rsa` (private) and `~/.ssh/id_rsa.pub` (public).

#### **Step 2: Copy the Public Key to the Remote Server**
Copy the public key to the remote server using:
```bash
ssh-copy-id user@remote_host
```

#### **Step 3: Disable Password Authentication (Optional but recommended)**
After confirming key-based login works, disable password authentication by editing the server configuration (`/etc/ssh/sshd_config`):
```bash
PasswordAuthentication no
```
Then restart the SSH service:
```bash
sudo systemctl restart sshd
```

---

### **5. Securing SSH**

#### **Change the Default SSH Port**
Modify the `Port` directive in `/etc/ssh/sshd_config` to a non-standard port:
```bash
Port 2222
```
Restart the SSH service after the change.

#### **Use Strong Key Algorithms**
Ensure strong encryption algorithms by modifying `sshd_config`:
```bash
Ciphers aes256-ctr,aes192-ctr,aes128-ctr
KexAlgorithms curve25519-sha256@libssh.org
MACs hmac-sha2-512,hmac-sha2-256
```

#### **Disable Root Login**
Set the following in `sshd_config` to prevent root login:
```bash
PermitRootLogin no
```

#### **Use Fail2Ban for Brute-Force Protection**
Fail2Ban monitors SSH logs and blocks IPs after multiple failed login attempts. Install and configure:
```bash
sudo apt-get install fail2ban
```

---

### **6. SSH Tunneling**

#### **Local Port Forwarding**
Forward a local port to a remote server.
```bash
ssh -L local_port:localhost:remote_port user@remote_host
```

#### **Remote Port Forwarding**
Forward a port on the remote server to your local machine.
```bash
ssh -R remote_port:localhost:local_port user@remote_host
```

#### **Dynamic Port Forwarding**
Creates a SOCKS proxy for dynamic forwarding.
```bash
ssh -D local_port user@remote_host
```

---

### **7. Troubleshooting SSH Issues**

#### **Common Issues:**
- **Permission Denied**: Ensure the correct key is being used and permissions on `~/.ssh` and key files are correct (`chmod 700 ~/.ssh` and `chmod 600 ~/.ssh/id_rsa`).
- **Connection Timeout**: Ensure the server’s firewall allows SSH traffic on the specified port.
- **Too Many Authentication Failures**: Use the `-o` option to specify the correct identity file:
  ```bash
  ssh -o IdentitiesOnly=yes -i ~/.ssh/id_rsa user@host
  ```

#### **Debugging SSH**
Use verbose mode to debug SSH connections:
```bash
ssh -v user@host
```
For more detailed output, use `-vv` or `-vvv`.

---

### **8. Managing SSH Connections**

#### **Screen and tmux**
Use tools like `screen` or `tmux` to manage long-running SSH sessions.

- **screen**:  
  ```bash
  screen
  ```

- **tmux**:  
  ```bash
  tmux
  ```

---

This guide provides a comprehensive overview of SSH in Linux, from basic configurations to advanced security practices. By properly configuring SSH, you can enhance the security of remote access to your systems.

--- 

Feel free to customize and extend this document based on your environment or specific SSH use cases!
