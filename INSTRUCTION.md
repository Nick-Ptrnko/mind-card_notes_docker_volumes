# Mind Card Notes - Deployment Instructions

This guide provides step-by-step instructions for containerizing and deploying the Mind Card Notes application using Docker without Docker Compose.

---

### Step 1: Create a Docker Network
Create a custom Docker network so the application container can communicate with the database container:
```bash
docker network create mind-cards-net
```

---

### Step 2: Create and Start the MySQL Database Container
Run the MySQL container with an isolated volume, setting up the required environment variables and attaching it to the network:
```bash
docker run -d \
  --name mind_card_db \
  --network mind-cards-net \
  -e MYSQL_ROOT_PASSWORD=root_secure_password \
  -e MYSQL_DATABASE=mind_card_notes \
  -e MYSQL_USER=mysqluser \
  -e MYSQL_PASSWORD=0704 \
  -v mysql_data:/var/lib/mysql \
  mysql:8.0
```

---

### Step 3: Application Code Updates for File Storage
The application code has been updated so that file system interactions (such as `bio.txt` and `mind_card_backup.json`) are isolated inside a dedicated `data/` directory.

---

### Step 4: Build and Push Docker Images
Build the updated Docker image with multiple tags and push them to Docker Hub:
```bash
docker build -t nickptrnko/mind-card_notes:latest -t nickptrnko/mind-card_notes:1.2 .
docker push nickptrnko/mind-card_notes:latest
docker push nickptrnko/mind-card_notes:1.2
```

---

### Step 5: Configure Environment Variables
Ensure your `.env` file (located in the `data/` directory or project root) points the database host to the MySQL container name:
```env
DB_HOST=mind_card_db
DB_USER=mysqluser
DB_PASSWORD=0704
DB_NAME=mind_card_notes
```

---

### Step 6: Run the Application Container
Start the application container by mounting the local `data/` folder, connecting the environment file, and linking it to the Docker network:
```bash
docker run -d -p 8000:8000 \
  --env-file data/.env \
  -v $(pwd)/data:/app/data \
  --name mind-cards-app \
  --network mind-cards-net \
  nickptrnko/mind-card_notes:latest
```

---

### Step 7: Migrate Data from Local Host to Containerized MySQL
If you have an existing database on your local host, you can transfer it to the container:

1. **Export a backup (dump) on your host machine:**
   ```bash
   mysqldump -u mysqluser -p mind_card_notes > mind_card_backup.sql
   ```

2. **Copy the backup file into the MySQL container:**
   ```bash
   docker cp mind_card_backup.sql mind_card_db:/tmp/mind_card_backup.sql
   ```

3. **Import the data inside the container:**
   ```bash
   docker exec -it mind_card_db mysql -u mysqluser -p mind_card_notes
   ```
   *Inside the MySQL shell, run:*
   ```sql
   source /tmp/mind_card_backup.sql;
   EXIT;
   ```