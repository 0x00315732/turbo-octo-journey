
---

## 🐳 Persistent Ubuntu Docker Container Setup

### **1. Create a Docker volume for persistent data**

```bash
sudo docker volume create ubuntu-root
```

This volume will persist your container’s `/root` directory across restarts.

---

### **2. Create and run the Ubuntu container, mounting the volume**

```bash
sudo docker run -it --name ubuntu-container -v ubuntu-root:/root ubuntu:latest
```

This starts an Ubuntu container and mounts the volume to `/root`.

---

### **3. Inside the container: Update & install packages**

```bash
apt update -y
```

```bash
apt install -y adduser sudo
```

```bash
adduser user
```

```bash
exit
```

The container stops but its state and volume are saved.

---

### **5. Restart the container**

```bash
sudo docker start ubuntu-container
```

---

### **6. Reattach to the running container**

```bash
sudo docker attach ubuntu-container
```

To detach from it without stopping:

- Press `Ctrl + P`, then `Ctrl + Q`

---

Login as root user

```bash
sudo docker exec -it ubuntu-container /bin/bash
```

Login as regular user

```bash
sudo docker exec -it --user user ubuntu-container /bin/bash 
```