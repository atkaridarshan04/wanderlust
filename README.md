# Wanderlust Application Dockerization

This file outlines the steps to dockerize the Wanderlust application, which consists of a frontend and backend service using MongoDB and Redis as databases.

## Prerequisites

- Docker installed on your machine
- Docker Compose installed (for the automated setup)

## Project Structure

```
project-root/
│
├── frontend/
│   └── Dockerfile
│
├── backend/
│   └── Dockerfile
│
├── data/
│   └── sample_posts.json
│
└── docker-compose.yml
```

## Manual Dockerization Steps

### 1. Build Docker Images

Navigate to the `frontend` and `backend` directories and build the Docker images:

```bash
cd frontend
docker build -t frontend .

cd ../backend
docker build -t backend .
```

### 2. Create a Docker Network

Create a Docker network named `wanderlust` to allow the containers to communicate with each other:

```bash
docker network create wanderlust
```

### 3. Run Database Containers

Run MongoDB and Redis containers on the created network:

```bash
# Run MongoDB
docker run --name mongo --network=wanderlust -p 27017:27017 -v ~/opt/data:/data/wanderlust -d mongo:latest

# Run Redis
docker run --name redis --network=wanderlust -p 6379:6379 -d redis:latest
```

### 4. Run Application Containers

Run the frontend and backend containers:

```bash
# Run Frontend
docker run --name frontend --network=wanderlust -p 5173:5173 -d frontend

# Run Backend
docker run --name backend --network=wanderlust -p 5000:5000 -d backend
```

### 5. Import Sample Data

Copy the sample data file into the MongoDB container and import it into the database:

```bash
# Copy sample data to MongoDB
docker cp data/sample_posts.json mongo:/data/

# Access MongoDB shell
docker exec -it mongo /bin/bash

# Import data
mongoimport --db wanderlust --collection posts --file ./data/sample_posts.json --jsonArray
```

### 6. Access the Application

You can access the application through the following URLs:

- For remote instances:
  ```
  http://<instance-ip>:5173
  ```
- For local testing:
  ```
  http://localhost:5173
  ```

## Automating with Docker Compose

To automate the setup, you can use Docker Compose. Make sure you have a `docker-compose.yml` file configured correctly.

### 1. Start Services

Run the following command to start all services in detached mode:

```bash
docker-compose up -d
```

### 2. Import Sample Data

After starting the services, copy the sample data file and import it into the MongoDB container (as described in the manual steps above):

```bash
# Copy sample data to MongoDB
docker cp data/sample_posts.json mongo:/data/

# Access MongoDB shell
docker exec -it mongo /bin/bash

# Import data
mongoimport --db wanderlust --collection posts --file ./data/sample_posts.json --jsonArray
```

### 3. Note on Data Reflection

Sometimes, it may take time for changes to reflect. You can create a test post in your application to verify that everything is working correctly.

---
