
---

## 🐳 Persistent Ubuntu Docker Container Setup

### **1. Create a Docker volume for persistent data**

```bash
docker volume create ubuntu-root
```

This volume will persist your container’s `/root` directory across restarts.

---

### **2. Create and run the Ubuntu container, mounting the volume**

```bash
docker run -it --name ubuntu-container -v ubuntu-root:/root ubuntu
```

This starts an Ubuntu container and mounts the volume to `/root`.

---

### **3. Inside the container: Update & install packages**

```bash
apt update && apt -y upgrade
apt install -y <package-name>
```

Example:

```bash
apt install -y net-tools curl vim
```

---

### **4. Exit the container to save changes**

```bash
exit
```

The container stops but its state and volume are saved.

---

### **5. Restart the container**

```bash
docker start ubuntu-container
```

---

### **6. Reattach to the running container**

```bash
docker attach ubuntu-container
```

To detach from it without stopping:

- Press `Ctrl + P`, then `Ctrl + Q`

---

## 🔧 Additional Docker Tips

### Mount more volumes (optional)

```bash
-v my-data:/data
```

Mount custom volumes for things like projects or logs.

### Expose ports (for running services)

```bash
-p 8080:80
```

Expose port 80 inside the container as 8080 on the host.

### Detached mode (run in background)

```bash
docker run -dit --name ubuntu-container -v ubuntu-root:/root ubuntu
```

### Automatically remove container on exit (non-persistent)

```bash
docker run --rm -it ubuntu
```

---