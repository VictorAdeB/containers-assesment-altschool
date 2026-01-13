
 # MuchToDo API  A robust RESTful API for a ToDo application built with **Golang**, using **MongoDB** for storage. The API supports user authentication, JWT-based sessions, and full CRUD operations for task management.  This guide provides instructions for running the project locally, via Docker Compose, or on a local Kubernetes cluster using Kind.  
 ---  ## 📋 Table of Contents  
 - [Prerequisites](#-prerequisites) 
  - [Local Setup (Windows 11)](#1-local-setup-windows-11) 
   - [Running with Docker Compose](#2-running-with-docker-compose)  
   - [Running on Kubernetes (Kind)](#3-running-on-kubernetes-kind)  
   - [API Endpoints](#4-api-endpoints)  - [Cleaning Up](#5-cleaning-up)  
   ---  ## 🛠 Prerequisites 
    Before running the project, ensure you have the following installed:  * **Go 1.21+** ([Download](https://go.dev/doc/install))  
    * **MongoDB** (Optional if using Docker)  
    * **Docker Desktop** (Windows 11 with WSL2 backend recommended)  
    * **Kind** (Kubernetes in Docker)  
    * **Swag CLI** for API documentation:      
    ```bash      go install [github.com/swaggo/swag/cmd/swag@latest](https://github.com/swaggo/swag/cmd/swag@latest)      ```  ---  ## 1️⃣ Local Setup (Windows 11) 

 ### Step 1: Clone the repository  ```bash  git clone   cd container-assessment/MuchToDo   `

### Step 2: Configure Environment Variables

Create a .env file in the project root:

Code snippet

`   PORT=8080  MONGO_URI=mongodb://localhost:27017  DB_NAME=much_todo_db  JWT_SECRET_KEY="your-super-secret-key-that-is-long-and-random"  JWT_EXPIRATION_HOURS=72  ENABLE_CACHE=false  LOG_LEVEL="DEBUG"  LOG_FORMAT="json"   `

### Step 3: Install Dependencies & Generate Docs

Bash

`   go mod tidy  swag init -g ./cmd/main.go -o ./docs   `

### Step 4: Run the API

Bash

`   go run ./cmd/main.go   `

*   **API Base:** http://localhost:8080
    
*   **Swagger Docs:** http://localhost:8080/swagger/index.html
    
*   **Health Check:** http://localhost:8080/health
    

2️⃣ Running with Docker Compose
-------------------------------

This is the recommended approach for local development to ensure environment parity.

### Step 1: Create .env.docker

Ensure your MONGO\_URI points to the service name defined in your compose file:

Code snippet
`   MONGO_URI=mongodb://mongodb:27017  # ... keep other variables same as .env   `

### Step 2: Build and Start

Bash

`   docker compose up -d --build   `

### Step 3: Verify

*   **Check Logs:** docker compose logs -f
    
*   **Access API:** http://localhost:8080/ping
    

3️⃣ Running on Kubernetes (Kind)
--------------------------------

### Step 1: Create Kind Cluster

Bash

`   kind create cluster --name muchtodo   `

### Step 2: Deploy Infrastructure

Bash

`   # Deploy MongoDB (Deployment, Service, PVC, ConfigMap, Secret)  kubectl apply -f kubernetes/mongodb/  # Deploy Backend API  kubectl apply -f kubernetes/backend/  # Deploy Ingress (Requires an Ingress Controller installed in Kind)  kubectl apply -f kubernetes/ingress.yaml   `

### Step 3: Check Status

Bash

`   kubectl get pods,svc,ingress -n muchtodo   `

4️⃣ API Endpoints
-----------------

**MethodEndpointDescription**GET/pingConnectivity testGET/healthSystem health statusPOST/usersRegister a new userPOST/users/loginAuthenticate and get JWTGET/todosList all user todosPOST/todosCreate a new todoPATCH/todos/:idUpdate an existing todoDELETE/todos/:idRemove a todo

> **Note:** For full schema details and request/response examples, visit the Swagger UI at /swagger/index.html.

5️⃣ Cleaning Up
---------------

**Docker Compose:**

Bash
`   docker compose down -v   `

**Kind Cluster:**

Bash
`   kind delete cluster --name muchtodo   `

💡 Notes
--------

*   **Port Conflicts:** If running MongoDB locally, stop the service before starting Docker Compose to avoid port 27017 or 27018 conflicts.
    
*   **Documentation:** Always run swag init after changing your API handlers or models to keep the documentation in sync.