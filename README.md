# File Store Service

This repository contains a File Store Service that allows users to store, retrieve, update, and perform operations on text files using an HTTP server and a CLI client. Additionally, the project includes Kubernetes manifests for deploying the service to a Kubernetes/OpenShift cluster.

## Features

### Part 1: File Store Service

1. **Add Files to the Store:**
   - Command: `store add file1.txt file2.txt`
   - Description: Upload files to the server. Fails if the file already exists.
   - Bonus: Optimized to avoid uploading duplicate files.

2. **List Files in the Store:**
   - Command: `store ls`
   - Description: List all files stored on the server.

3. **Remove a File:**
   - Command: `store rm file.txt`
   - Description: Remove a file from the server.

4. **Update Contents of a File:**
   - Command: `store update file.txt`
   - Description: Update the contents of a file or create it if absent.

5. **File Operations:**
   - **Word Count:** `store wc` returns the total word count across all files.
   - **Frequent Words:** `store freq-words [--limit|-n 10] [--order=dsc|asc]` returns the most/least frequent words.

### Part 2: Kubernetes/OpenShift Deployment
- Kubernetes manifests provided to deploy the File Store Service to a Kubernetes cluster using Minikube or Kind.

---

## Lab Steps

### Prerequisites

1. **Environment Setup:**
   - Install Docker.
   - Install Minikube or Kind for local Kubernetes cluster.
   - Install `kubectl` CLI.
   - Install Python 3.x or Node.js (based on your chosen language).

2. **Clone the Repository:**
   ```bash
   git clone <repository-url>
   cd file-store-service
   ```

---

### Step 1: Develop the File Store Service

#### 1.1 Server Implementation

1. **Install Dependencies:**
   ```bash
   pip install flask flask-restful
   ```

2. **Run the Server:**
   ```bash
   python server/app.py
   ```

#### 1.2 Client Implementation

1. **Install Dependencies:**
   ```bash
   pip install requests
   ```

2. **Run the Client:**
   ```bash
   python client/cli.py
   ```

---

### Step 2: Test the Service

#### Commands

1. **Add Files:**
   ```bash
   python client/cli.py add file1.txt file2.txt
   ```

2. **List Files:**
   ```bash
   python client/cli.py ls
   ```

3. **Remove a File:**
   ```bash
   python client/cli.py rm file.txt
   ```

4. **Update File:**
   ```bash
   python client/cli.py update file.txt
   ```

5. **Word Count:**
   ```bash
   python client/cli.py wc
   ```

6. **Frequent Words:**
   ```bash
   python client/cli.py freq-words --limit 5 --order asc
   ```

---

### Step 3: Containerize the Application

1. **Create Dockerfile for Server:**
   ```dockerfile
   FROM python:3.9-slim
   WORKDIR /app
   COPY ./server /app
   RUN pip install -r requirements.txt
   CMD ["python", "app.py"]
   ```

2. **Build and Push Docker Image:**
   ```bash
   docker build -t <dockerhub-username>/file-store-service:latest .
   docker push <dockerhub-username>/file-store-service:latest
   ```

---

### Step 4: Deploy to Kubernetes

#### 4.1 Prepare Kubernetes Manifests

1. **Deployment YAML:**
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: file-store-service
   spec:
     replicas: 2
     selector:
       matchLabels:
         app: file-store-service
     template:
       metadata:
         labels:
           app: file-store-service
       spec:
         containers:
         - name: file-store-service
           image: <dockerhub-username>/file-store-service:latest
           ports:
           - containerPort: 5000
   ```

2. **Service YAML:**
   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: file-store-service
   spec:
     type: NodePort
     selector:
       app: file-store-service
     ports:
       - protocol: TCP
         port: 80
         targetPort: 5000
   ```

#### 4.2 Deploy to Cluster

1. **Start Minikube:**
   ```bash
   minikube start
   ```

2. **Apply Manifests:**
   ```bash
   kubectl apply -f deployment.yaml
   kubectl apply -f service.yaml
   ```

3. **Access the Service:**
   ```bash
   minikube service file-store-service
   ```

---

### Step 5: Verify and Test

1. **Test Endpoints:**
   Use `curl` or Postman to interact with the service.

2. **Client Interaction:**
   Point the CLI client to the Kubernetes service URL.

---

## Bonus

1. **Git Best Practices:**
   - Use meaningful commit messages.
   - Ensure commits show incremental progress.

2. **Parallel Processing:**
   Optimize file operations to handle large datasets efficiently.

3. **Resilience:**
   Implement retry mechanisms for network interruptions.

---

## Repository Structure

```
file-store-service/
├── client/
│   ├── cli.py
│   └── requirements.txt
├── server/
│   ├── app.py
│   └── requirements.txt
├── deployment.yaml
├── service.yaml
├── Dockerfile
└── README.md
```

---

## Next Steps

1. Push all code, configurations, and documentation to a GitHub repository.
2. Share the repository link with reviewers.
3. Schedule a discussion to present the solution.
