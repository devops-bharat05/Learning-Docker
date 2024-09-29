# Attaching EBS Volume to an Ubuntu EC2 Instance and Configuring Docker Data Storage

## Objective
This guide explains how to create and attach an EBS volume to an Ubuntu-based EC2 instance and map the EBS volume to store Docker data. By following this step-by-step tutorial, you will configure Docker to store data on the attached EBS volume.

## Prerequisites
- An **EC2 instance** running **Ubuntu**.
- **Docker** installed on the EC2 instance.
- **EBS Volume** created and located in the same availability zone as your EC2 instance.

## Steps

### 1. Launch the EC2 Instance and Install Docker

1. **Update the package list:**
   ```bash
   sudo apt-get update
   ```
   
2. **Install Docker:**
   ```bash
   sudo apt install docker -y
   ```

3. **Start Docker:**
   ```bash
   sudo systemctl start docker
   sudo systemctl enable docker
   sudo systemctl status docker
   ```

4. **Check the available disk space:**
   ```bash
   sudo df -h
   ```

### 2. Create and Attach the EBS Volume

1. **Create an EBS volume** in the AWS Console. Make sure it is in the same availability zone as your EC2 instance.

2. **Attach the EBS volume** to your EC2 instance.

3. **Verify if the volume is attached:**
   ```bash
   lsblk
   ```
   You should see an extra volume, such as `xvdf`.

### 3. Format and Mount the EBS Volume

1. **Format the EBS volume:**
   ```bash
   sudo fdisk /dev/xvdf
   ```

2. **Verify the newly formatted volume:**
   ```bash
   lsblk
   ```

3. **Create a filesystem:**
   ```bash
   sudo mkfs.ext4 /dev/xvdf1
   ```

4. **Copy the UUID of the volume:**
   After formatting, an output will provide the UUID, for example:  
   `UUID: 0a34a11f-87d3-40e1-b845-2af06791d14e`.

5. **Create a directory for the volume mount (in this case, `/dockerdata`):**
   ```bash
   sudo mkdir /dockerdata
   ```

6. **Edit the `/etc/fstab` file to automatically mount the EBS volume at startup:**
   ```bash
   sudo vim /etc/fstab
   ```
   Add the following line:
   ```
   UUID=0a34a11f-87d3-40e1-b845-2af06791d14e /dockerdata ext4 defaults 0 0
   ```

7. **Mount the volume:**
   ```bash
   sudo mount -a
   ```

8. **Verify the mounted volume:**
   ```bash
   lsblk
   ```

### 4. Configure Docker to Use the EBS Volume

1. **Stop Docker services:**
   ```bash
   sudo systemctl stop docker.service
   sudo systemctl stop docker.socket
   ```

2. **Edit Docker’s service configuration file:**
   ```bash
   sudo vim /lib/systemd/system/docker.service
   ```
   Modify the `ExecStart` line to point to the new data directory (`/dockerdata`):
   ```
   ExecStart=/usr/bin/dockerd --data-root /dockerdata -H fd:// --containerd=/run/containerd.sock
   ```

3. **Move existing Docker data to the new volume:**
   ```bash
   sudo rsync -aqxP /var/lib/docker/ /dockerdata
   ```

4. **Reload the Docker daemon and restart Docker:**
   ```bash
   sudo systemctl daemon-reload
   sudo systemctl start docker
   ```

5. **Verify the Docker service status:**
   ```bash
   sudo systemctl status docker --no-pager
   ```

6. **Check if Docker is running:**
   ```bash
   ps aux | grep -i docker | grep -v grep
   ```

### 5. Test Docker Setup

1. **Run a test Docker container to ensure everything is working:**
   ```bash
   docker run --rm -d --name app1 -p 8000:80 kiran2361993/troubleshootingtools:v1
   ```

2. **Verify the disk space:**
   ```bash
   df -h
   ```

## Conclusion

You have successfully attached an EBS volume to your Ubuntu EC2 instance, configured it to store Docker data, and ensured Docker is using the newly mounted volume. This setup helps in managing Docker’s data and scaling storage independently of your EC2 instance’s root volume.

## Troubleshooting

- Ensure that the EC2 instance and the EBS volume are in the same availability zone.
- If the Docker service fails to start after the configuration changes, verify the `ExecStart` parameters in the Docker service file.
- Ensure the EBS volume is properly mounted by checking `/etc/fstab` and running `mount -a` to resolve any mounting issues.

For any further assistance, please consult the AWS documentation or Docker official guides.
