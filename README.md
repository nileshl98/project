# File Store Service

The **File Store Service** is a simple HTTP-based file management system built using Flask. It supports various operations such as adding, updating, removing, listing files, counting words, and listing the most frequent words across all files stored on the server.

Additionally, the service can be deployed on a Kubernetes/OpenShift cluster using Minikube or kind for local clusters.

---

## Part 1: The File Store Service

### Features

- **Add Files**: Upload files to the server using:
  ```bash
  store add file1.txt file2.txt
  ```
- **List Files**: View the list of files stored on the server using:
  ```bash
  store ls
  ```
- **Remove Files**: Delete files from the server with:
  ```bash
  store rm file.txt
  ```
- **Update Files**: Replace the content of an existing file with:
  ```bash
  store update file.txt
  ```
- **Word Count**: Get the total word count of all files stored on the server using:
  ```bash
  store wc
  ```
- **Frequent Words**: List the most frequent words across all stored files, sorted by frequency with:
  ```bash
  store freq-words
  ```

---

### Requirements

To run this service, you need:

- Python 3.x installed
- Flask for the web server
- Docker (optional, for containerization)

Install dependencies:
```bash
pip install -r requirements.txt
```

---

### Installation

1. Clone the repository:
    ```bash
    git clone https://github.com/omdeshmukh24/Filestore.git
    cd Filestore
    ```

2. (Optional) Set up a virtual environment:
    ```bash
    python3 -m venv venv
    source venv/bin/activate  # On Windows use: venv\Scripts\activate
    ```

3. Install the dependencies:
    ```bash
    pip install -r requirements.txt
    ```

4. Run the server:
    ```bash
    python server.py
    ```

The server will start and listen on port 5000. Access it at [http://127.0.0.1:5000](http://127.0.0.1:5000).

---

### API Endpoints

#### Add Files
**POST** `/files/add`

Upload a file to the server.

**Request:**
```bash
curl -X POST -F "file=@file1.txt" http://127.0.0.1:5000/files/add
```
**Response:**
```json
{"message": "File added successfully"}
```

#### List Files
**GET** `/files/list`

List all files stored on the server.

**Request:**
```bash
curl http://127.0.0.1:5000/files/list
```
**Response:**
```json
{"files": ["file1.txt", "file2.txt"]}
```

#### Remove File
**DELETE** `/files/remove`

Remove a file from the server.

**Request:**
```bash
curl -X DELETE -d "file=file1.txt" http://127.0.0.1:5000/files/remove
```
**Response:**
```json
{"message": "File removed successfully"}
```

#### Update File
**PUT** `/files/update`

Update the content of an existing file or add it if it doesn’t exist.

**Request:**
```bash
curl -X PUT -F "file=@file1.txt" http://127.0.0.1:5000/files/update
```
**Response:**
```json
{"message": "File updated successfully"}
```

#### Word Count
**GET** `/files/stats`

Get the total word count of all files stored.

**Request:**
```bash
curl http://127.0.0.1:5000/files/stats
```
**Response:**
```json
{"total_words": 150}
```

#### Frequent Words
**GET** `/files/freq-words`

Get the most frequent words across all files.

**Request:**
```bash
curl http://127.0.0.1:5000/files/freq-words
```
**Response:**
```json
{"words": [{"word": "the", "count": 15}, {"word": "is", "count": 12}]}
```

---

## Part 2: Kubernetes/OpenShift Deployment

### Overview

In this part, the goal is to deploy the File Store Service on a Kubernetes or OpenShift cluster. This involves using Minikube or kind for local clusters, and Kubernetes manifests to deploy the application.

### Requirements for Deployment

- Minikube or kind for creating a local Kubernetes cluster.
- kubectl to interact with the cluster.
- Docker to build container images.

---

### Steps for Kubernetes Deployment

#### 1. Set up Kubernetes Cluster with Minikube

1. **Install Minikube**: Follow the instructions on [Minikube documentation](https://minikube.sigs.k8s.io/docs/).
2. **Start Minikube**:
    ```bash
    minikube start
    ```

#### 2. Build Docker Image

Build the Docker image for the file store service:
```bash
docker build -t filestore-service .
```

#### 3. Create Kubernetes Manifests

Create `deployment.yaml` and `service.yaml` to deploy the service.

**Example `deployment.yaml`:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: filestore-service
spec:
  replicas: 1
  selector:
    matchLabels:
      app: filestore-service
  template:
    metadata:
      labels:
        app: filestore-service
    spec:
      containers:
      - name: filestore-service
        image: filestore-service
        ports:
        - containerPort: 5000
```

**Example `service.yaml`:**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: filestore-service
spec:
  selector:
    app: filestore-service
  ports:
  - protocol: TCP
    port: 5000
    targetPort: 5000
  type: LoadBalancer
```

#### 4. Deploy to Kubernetes

Apply the manifests to deploy the service:
```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

#### 5. Access the Application

Use `kubectl` to find the external IP or use port-forwarding to access the service:
```bash
kubectl port-forward svc/filestore-service 5000:5000
```

---

### Dockerization (Optional)

Run the service as a Docker container using the following steps:

1. **Build the Docker image:**
    ```bash
    docker build -t filestore-service .
    ```

2. **Run the Docker container:**
    ```bash
    docker run -p 5000:5000 filestore-service
    ```

The service will be available at [http://localhost:5000](http://localhost:5000).
