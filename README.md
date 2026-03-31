🚀 Kubernetes Node.js Deployment using Minikube
This guide demonstrates how to containerize a simple Node.js web application and deploy it on a Kubernetes cluster using Minikube.

🧠 1️⃣ Understanding Kubernetes Components
App → Container → Pod → Node → Cluster
Component	Description
App	Your Node.js application source code
Container	The Docker image that packages the app and its dependencies
Pod	The smallest deployable unit in Kubernetes (can hold one or more containers)
Node	A machine (VM or physical) that runs one or more pods
Cluster	A group of nodes managed by Kubernetes
💡 Note: A Pod is the single deployment unit in Kubernetes.

⚙️ 2️⃣ Setup Minikube & Verify Environment
🧩 Start Minikube
minikube status
# OR
minikube start --driver=docker

🔧 Enable Addons & Dashboard
minikube addons enable metrics-server
minikube dashboard
🔍 Verify Node
kubectl get nodes

3️⃣ Create & Test Node.js Application
Check Installed Versions
node -v
npm -v
git --version
Clone the Project Repository
git clone https://github.com/atulkamble/nodehelloworld.git
cd nodehelloworld
Open in VS Code
Open folder → k8s-nodejs-hello-world
Open integrated terminal
Run App Locally
cd app
pwd
node server.js
Visit 👉 http://localhost:3000


4️⃣ Project Structure
k8s-nodejs-hello-world/
├── app/
│   ├── Dockerfile
│   └── server.js
├── k8s/
│   ├── deployment.yaml
│   └── service.yaml
├── .dockerignore
├── .gitignore
└── README.md

5️⃣ Application Files
📄 app/server.js
const http = require('http');
const port = process.env.PORT || 3000;

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello from Kubernetes!\n');
});

server.listen(port, () => {
  console.log(`Server running at http://localhost:${port}/`);
});
📄 app/Dockerfile
FROM node:18-alpine

WORKDIR /app
COPY server.js .

EXPOSE 3000
CMD ["node", "server.js"]
📄 .dockerignore
node_modules
npm-debug.log


6️⃣ Build & Push Docker Image
Build Image
sudo docker build -t docker.io/atuljkamble/nodehelloworld .
Verify Image
sudo docker images
Push Image to Docker Hub
sudo docker push docker.io/atuljkamble/nodehelloworld
Test Container Locally
sudo docker run -d -p 3000:3000 docker.io/atuljkamble/nodehelloworld
Manage Containers
sudo docker container ls
sudo docker container stop <container-id>
# Example:
sudo docker container stop fe8dcffe1ee1


7️⃣ Create Kubernetes Manifests
📄 k8s/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-node-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: hello-node
  template:
    metadata:
      labels:
        app: hello-node
    spec:
      containers:
      - name: hello-node
        image: docker.io/atuljkamble/nodehelloworld
        ports:
        - containerPort: 3000
📄 k8s/service.yaml
apiVersion: v1
kind: Service
metadata:
  name: hello-node-service
spec:
  type: LoadBalancer
  selector:
    app: hello-node
  ports:
    - protocol: TCP
      port: 80
      targetPort: 3000


8️⃣ Deploy to Kubernetes (Minikube)
🧠 Note:

In AWS EKS → use LoadBalancer
In Minikube → use NodePort or minikube tunnel
Apply Deployment & Service
cd k8s
kubectl apply -f deployment.yaml
kubectl apply -f minikube-service.yaml
Verify Deployment
kubectl get deployments
kubectl get pods
kubectl get services


9️⃣ Access the Application
Start Minikube Tunnel
minikube tunnel   # keep running
Open Service in Browser
kubectl get services
minikube service hello-node-service
This opens the app in a browser 🎉


🔟 Stop & Clean Up
minikube stop
minikube delete
🧾 Summary Table
Step	Command	Purpose
1	minikube start --driver=docker	Start Kubernetes cluster
2	kubectl get nodes	Verify node connectivity
3	docker build -t <image>	Build Docker image
4	docker push <image>	Push to Docker Hub
5	kubectl apply -f deployment.yaml	Deploy application
6	minikube service hello-node-service	Access application
7	minikube delete	Cleanup environment


1️⃣1 README Summary (for GitHub)
# k8s-nodejs-hello-world

A basic Node.js web app deployed using Docker and Kubernetes (Minikube or EKS).

## Features
- Node.js HTTP server
- Dockerized and portable
- Kubernetes Deployment & Service YAMLs

## Quick Start
1. Build & Push Docker Image  
2. Apply Kubernetes configs  
3. Access via LoadBalancer or Minikube
