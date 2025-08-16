
1. Create a volume for the container:

```bash
sudo docker volume create kali-root
```

2. Create a container and mount the volume:

```bash
sudo docker run -it --name kali-container -v kali-root:/root kalilinux/kali-rolling:latest
```

3. Install any packages that you need:

```bash
apt update && apt -y install kali-linux-headless  
```

4. Save your changes:

```
exit
```

5. Start the container again:

```
sudo docker start kali-container
```

6. Attach to the Docker container :

```
sudo docker attach kali-container
```

7. Log into container

```
sudo docker exec -it kali-container /bin/bash
```