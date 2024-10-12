# 🚀 Deploying Wanderlust Application on Minikube

This guide will walk you through deploying the **Wanderlust** application, consisting of **MongoDB, Redis, Backend, and Frontend**, on **Minikube**.

### 1️⃣ Create the Namespace  
Start by creating a new Kubernetes namespace for the Wanderlust application:

```bash
kubectl create namespace wanderlust
```

---

### 2️⃣ Deploy the Services  
Deploy **MongoDB, Redis, Backend, and Frontend** into the `wanderlust` namespace using their respective YAML files.

```bash
kubectl apply -f mongodb.yml
kubectl apply -f redis.yml 
kubectl apply -f backend.yml
kubectl apply -f frontend.yml
```

After deployment, verify all services and pods are running:

```bash
kubectl get pods -n wanderlust
kubectl get svc -n wanderlust
```

---

### 3️⃣ Access the Application  
To access the application, get the **Minikube IP**:

```bash
minikube ip
```

Then, list the services to identify the **NodePort** for the frontend:

```bash
kubectl get svc -n wanderlust
```

Now, access the application using the Minikube IP and frontend’s NodePort:

```
http://<minikube-ip>:<frontend-nodeport>
```

For example:
```
http://192.168.58.2:30000
```

---

### 4️⃣ Import Data into MongoDB  

#### Step 1: Copy Data from the Backend Directory to MongoDB Pod  
The `sample_posts.json` file is already present in your backend folder at `/backend/data/sample_posts.json`. Use the following command to copy it into the MongoDB pod:

```bash
kubectl cp ./backend/data/sample_posts.json <mongo-pod>:/data/sample_posts.json -n wanderlust
```

> Replace `<mongo-pod>` with the name of the running MongoDB pod, which you can find using:
```bash
kubectl get pods -n wanderlust
```

#### Step 2: Exec into the MongoDB Pod  
Open an interactive shell inside the MongoDB pod:

```bash
kubectl exec -it <mongo-pod> -n wanderlust -- /bin/bash
```

#### Step 3: Import Data into the `wanderlust` Database  
Run the following command inside the MongoDB pod to import the data:

```bash
mongoimport --db wanderlust --collection posts --file /data/sample_posts.json --jsonArray
```

---

### 5️⃣ Verify the Data in the Application  
1. Open the frontend in your browser using the **Minikube IP** and **NodePort**.
2. Create a **new post** using the frontend interface.
3. Verify that the newly created post is displayed along with the posts imported from the `sample_posts.json` file.

---

Congratulations! 🎉 You have successfully deployed the Wanderlust application on Minikube and imported data into MongoDB. Now you can interact with the app, create posts, and see them reflected in real-time.