# MSc DE1 Distributed Systems: Flask Application Containerization & Kubernetes Orchestration

[![Docker Hub](https://img.shields.io/badge/Docker%20Hub-harineshmk%2Fmsc--de1--flask--app-blue?logo=docker)](https://hub.docker.com/r/harineshmk/msc-de1-flask-app)
[![Kubernetes](https://img.shields.io/badge/Kubernetes-Kind%20Cluster-326CE5?logo=kubernetes)](https://kind.x-k8s.io/)
[![Python](https://img.shields.io/badge/Python-3.11--slim-3776AB?logo=python)](https://www.python.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

This repository contains an enhanced RESTful Python Flask application with automated unit testing, multi-stage Docker containerization, non-root security hardening (`UID 10001`), Docker Scout vulnerability auditing, and multi-node Kubernetes orchestration on a Kind cluster.

---

## 📌 Project Metadata & Credentials

* **Student Name:** Harinesh Muthu Kumar
* **Academic Program:** MSc Cloud Computing & Data Engineering
* **Course:** MSc DE1 Distributed Systems
* **Docker Hub Repository:** [`harineshmk/msc-de1-flask-app`](https://hub.docker.com/r/harineshmk/msc-de1-flask-app)
* **GitHub Repository:** [`msc-de1-distributed-systems-docker-k8s`](https://github.com/Hari9489/msc-de1-distributed-systems-docker-k8s)

---

## 🌐 Application REST API Endpoints

The core Python Flask application serves a RESTful API with the following routes:

| HTTP Method | Route | Description |
| :--- | :--- | :--- |
| `GET` | `/` | Returns primary service greeting (`Hello, Flask!`) |
| `GET` | `/items` | Returns a list of all items |
| `GET` | `/items/{item_id}` | Returns details for a specific item |
| `POST` | `/items` | Adds a new item to the data collection |

---

## 📂 Project Directory Structure

```text
msc-de1-distributed-systems-docker-k8s/
├── app/                        # Flask application source code and routes
│   ├── __init__.py
│   └── routes.py
├── docs/                       
│   └── Report.pdf              # Technical report 
├── evidence/                   
│   ├── container_uid.txt
│   ├── healthcheck.txt
│   ├── k8s_deployment.txt
│   ├── k8s_endpoint_response.txt
│   └── k8s_pods.txt
├── k8s/                        # Declarative Kubernetes manifests
│   ├── configmap.yaml          # Environment parameters
│   ├── deployment.yaml        # Pod deployment (2 replicas, non-root UID 10001)
│   └── service.yaml           # NodePort service exposure (Port 30080)
├── kind/                       # Multi-node cluster configuration
│   └── cluster-config.yaml     # 3-node topology (1 control-plane, 2 workers)
├── security/                   # Docker Scout audit outputs & SBOM
│   ├── docker_scout_vulns.txt
│   └── sbom.json
├── tests/                      # Application unit testing suite
├── .dockerignore
├── .gitignore
├── compose.yaml                # Local Docker Compose setup
├── Dockerfile                  # Production hardened Dockerfile
├── LICENSE                     # MIT License
├── README.md                   # Project landing page
├── requirements.txt            # Python dependencies
└── run.py                      # Application entry point

🛠️ Executed Workflow & Step-by-Step Commands
Phase 1: Local Development & Unit Testing
Clone the repository:

1. PowerShell
git clone [https://github.com/Hari9489/msc-de1-distributed-systems-docker-k8s.git](https://github.com/Hari9489/msc-de1-distributed-systems-docker-k8s.git)
cd msc-de1-distributed-systems-docker-k8s
Set up virtual environment & install dependencies:

2. PowerShell
python -m venv venv
.\venv\Scripts\activate
pip install -r requirements.txt
Execute unit tests:

3. PowerShell
python -m unittest discover tests
Run application locally:

4. PowerShell
python run.py
# Accessible at [http://127.0.0.1:5000/](http://127.0.0.1:5000/)

Phase 2: Containerization & Security Hardening
Build hardened Docker container image:

1. PowerShell
docker build -t msc-flask-app:1.0.0 .
Run container locally with port binding:

2. PowerShell
docker run -d -p 5000:5000 --name test_flask_app msc-flask-app:1.0.0
Verify container health & endpoint response:

3. PowerShell
docker ps --filter "name=test_flask_app"
curl.exe [http://127.0.0.1:5000/](http://127.0.0.1:5000/)

Phase 3: Vulnerability Scanning & Registry Publishing
Run Docker Scout security audit & generate SBOM:

1. PowerShell
docker scout cves msc-flask-app:1.0.0 > security/docker_scout_vulns.txt
docker scout sbom msc-flask-app:1.0.0 --output security/sbom.json
Tag and push image to Docker Hub:

2. PowerShell
docker tag msc-flask-app:1.0.0 harineshmk/msc-de1-flask-app:1.0.0
docker tag msc-flask-app:1.0.0 harineshmk/msc-de1-flask-app:latest
docker push harineshmk/msc-de1-flask-app:1.0.0
docker push harineshmk/msc-de1-flask-app:latest

Phase 4: Multi-Node Kubernetes Orchestration
Provision 3-node Kind cluster (1 control-plane, 2 workers):

1. PowerShell
kind create cluster --config kind/cluster-config.yaml --name msc-cluster
Apply Kubernetes declarative manifests:

2. PowerShell
kubectl apply -f k8s/configmap.yaml
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
Verify pod scheduling across worker nodes:

3. PowerShell
kubectl get pods -o wide
Verify non-root process execution inside pod:

4. PowerShell
$podName = (kubectl get pods -l app=flask-app -o jsonpath='{.items[0].metadata.name}')
kubectl exec $podName -- id
# Output: uid=10001(appuser) gid=10001(appgroup)
Test Kubernetes service endpoint routing:

5. PowerShell
kubectl port-forward svc/flask-service 8080:5000
curl.exe -i [http://127.0.0.1:8080/](http://127.0.0.1:8080/)

License & Acknowledgments
Original Sample App Base: Created by Pan Luo under the MIT License.

Cloud-Native & Kubernetes Implementation: Developed by Harinesh Muthu Kumar for the MSc Cloud Computing & Data Engineering program (MSc DE1 Distributed Systems course).
