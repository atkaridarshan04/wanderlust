# 🚀 Deploying Wanderlust Application on Minikube

This guide will walk you through deploying the **Wanderlust** application, consisting of **MongoDB, Redis, Backend, and Frontend**, on **Minikube**.

---

## 1️⃣ Create the Namespace  
Create a dedicated Kubernetes namespace for the Wanderlust application:

```bash
kubectl create namespace wanderlust
```

---

## 2️⃣ Deploy the Services  
Deploy the services into the `wanderlust` namespace using the YAML files:

```bash
kubectl apply -f mongodb.yml
kubectl apply -f redis.yml
kubectl apply -f backend.yml
kubectl apply -f frontend.yml
```

Verify that the pods and services are up and running:

```bash
kubectl get pods -n wanderlust
kubectl get svc -n wanderlust
```

---

## 3️⃣ Access the Application Using NodePort  

1. Get the **Minikube IP**:

   ```bash
   minikube ip
   ```

2. Identify the **NodePort** for the frontend:

   ```bash
   kubectl get svc -n wanderlust
   ```

   Example output:
   ```
   NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)         AGE
   frontend     NodePort    10.96.0.15      <none>        80:30000/TCP    2m
   ```

3. Access the application using the Minikube IP and frontend’s NodePort:

   ```
   http://<minikube-ip>:<frontend-nodeport>
   ```

   Example:
   ```
   http://192.168.58.2:30000
   ```

---

## 4️⃣ Configuring Ingress for Host-Based Routing  

### Step 1: Enable Ingress on Minikube  
Enable the Ingress addon:

```bash
minikube addons enable ingress
```

Check if the **Nginx Ingress Controller** pod is running:

```bash
kubectl get pods -A | grep nginx
```

If needed, view the logs of the Ingress controller:

```bash
kubectl logs <ingress-nginx-pod-name> -n ingress-nginx
```

Replace `<ingress-nginx-pod-name>` with the appropriate pod name (e.g., `ingress-nginx-controller-bc57996ff-bzf9h`).

---

### Step 2: Apply the Ingress Resource  
Deploy the ingress resource in the `wanderlust` namespace:

```bash
kubectl apply -f ingress.yml
```

Verify the ingress resource:

```bash
kubectl get ingress -n wanderlust
```

Example output:
```
NAME      CLASS   HOSTS            ADDRESS        PORTS   AGE
ingress   nginx   wanderlust.com   192.168.58.2   80      53s
```

---

### Step 3: Configure Local DNS for Domain Access  

Modify your `/etc/hosts` file to map the Minikube IP to the custom domain:

```bash
sudo vi /etc/hosts
```

Add the following line:

```
192.168.58.2 wanderlust.com
```

---

### Step 4: Access the Application Using the Domain  

Now, open the application in your browser at:

```
http://wanderlust.com
```

---

## 5️⃣ Import Data into MongoDB  

### Step 1: Copy Data to the MongoDB Pod  
The `sample_posts.json` file is available in the backend directory. Use the following command to copy it into the MongoDB pod:

```bash
kubectl cp ./backend/data/sample_posts.json <mongo-pod>:/data/sample_posts.json -n wanderlust
```

Find the MongoDB pod name:

```bash
kubectl get pods -n wanderlust
```

### Step 2: Exec into the MongoDB Pod  
Access the MongoDB pod via an interactive shell:

```bash
kubectl exec -it <mongo-pod> -n wanderlust -- /bin/bash
```

### Step 3: Import the Data into the `wanderlust` Database  

Run the following command inside the MongoDB pod:

```bash
mongoimport --db wanderlust --collection posts --file /data/sample_posts.json --jsonArray
```

---

## 6️⃣ Verify the Application  

1. **Using NodePort:**  
   Open the application via NodePort at:  
   `http://<minikube-ip>:<frontend-nodeport>`  
   Example: `http://192.168.58.2:30000`

2. **Using Ingress:**  
   Open the application via Ingress at:  
   `http://wanderlust.com`

3. Create a **new post** through the frontend.
4. Ensure the new post and imported posts from `sample_posts.json` are displayed correctly.

---

## 🎉 Congratulations!  
Congratulations! 🎉 You have successfully deployed the **Wanderlust application** on Minikube. Now you can interact with the app, create posts, and see them reflected in real-time.