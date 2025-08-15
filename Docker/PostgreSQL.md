
---
## 🐘 Persistent PostgreSQL Docker Container Setup

### **1. Create a Docker volume for persistent PostgreSQL data**

```bash
sudo docker volume create pgdata
```

This volume will persist PostgreSQL database data across container restarts.

---

### **2. Create and run the PostgreSQL container, mounting the volume**

```bash
sudo docker run -d \
  --name postgres-container \
  -e POSTGRES_USER=user \
  -e POSTGRES_PASSWORD=password \
  -e POSTGRES_DB=database \
  -v pgdata:/var/lib/postgresql/data \
  -p 5432:5432 \
  pgdata
```

- Replace:
    - `user` → Your PostgreSQL username
    - `password` → Your PostgreSQL password
    - `database` → Initial database name
- Volume `postgres` stores database files.

---

### **3. Connect to PostgreSQL inside the container**

```bash
docker exec -it postgres-container psql -U user -d database
```

You’ll enter the PostgreSQL interactive terminal (`psql`).

---

### **4. Exit `psql`**

```sql
\q
```

This returns you to the shell.

---

### **5. Stop the container**

```bash
docker stop postgres-container
```

Your database stays intact thanks to the volume.

---

### **6. Restart the container**

```bash
docker start postgres-container
```

PostgreSQL is now ready again.

---

### **7. (Optional) View container logs**

```bash
docker logs postgres-container
```

---

## 🔧 Additional Docker Tips for PostgreSQL

### Backup database to a file:

```bash
docker exec postgres-container pg_dump -U user database > backup.sql
```

### Restore database from a file:

```bash
cat backup.sql | docker exec -i postgres-container psql -U user -d database
```

### Run in interactive mode (rare for PostgreSQL):

```bash
docker run -it --rm postgres-container bash
```

---