
---

## 🐳 Persistent Kali Linux Docker Container Setup

### ✅ **Assumptions:**

- Docker is installed and running.
- You're using the official Kali Docker image: [`kalilinux/kali-rolling`](https://hub.docker.com/r/kalilinux/kali-rolling).

---

### **1. Create a Docker volume for persistent storage**

This will persist changes inside the container, especially for the `/root` directory (your Kali user's home directory):

```bash
docker volume create kali-root
```

---

### **2. Run the Kali Linux container with the volume mounted**

```bash
docker run -it --name kali-container -v kali-root:/root kalilinux/kali-rolling
```

- `-it`: interactive terminal
- `--name`: assigns a name to the container
- `-v`: mounts the volume to `/root`

---

### **3. Inside the container: update and install tools**

Update and install any Kali packages you want:

```bash
apt update && apt -y upgrade
apt install -y kali-linux-headless
apt install -y <your-tools>
```

For example:

```bash
apt install -y nmap metasploit-framework
```

---

### **4. Exit the container (to save and stop it)**

```bash
exit
```

Your container is stopped but not deleted.

---

### **5. Start the container again later**

```bash
docker start kali-container
```

---

### **6. Reattach to the running container**

```bash
docker attach kali-container
```

To **detach without stopping** the container:

> Press `Ctrl + P`, then `Ctrl + Q`

---

## 🔧 Additional Docker Options for Kali

### Expose Ports (e.g., for web servers, reverse shells, etc.)

```bash
docker run -it -p 4444:4444 --name kali-container -v kali-root:/root kalilinux/kali-rolling
```

This maps port `4444` on the container to `4444` on the host.

---

### Mount More Volumes

For example, mount a local tools directory:

```bash
docker run -it -v /home/user/tools:/tools -v kali-root:/root --name kali-container kalilinux/kali-rolling
```

---

### Run in Detached Mode (Background)

```bash
docker run -dit --name kali-container -v kali-root:/root kalilinux/kali-rolling
```

Then attach with:

```bash
docker attach kali-container
```

---

### Auto-remove (non-persistent session)

If you don’t need persistence:

```bash
docker run --rm -it kalilinux/kali-rolling
```

---

Copy to container 

```
sudo docker cp file.txt kali-container:/files/
```

---
