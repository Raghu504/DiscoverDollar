Frontend = React (or any JS frontend)

Backend = Node.js/Express

DB = MongoDB

Git hosting = GitHub

Images = Docker Hub

Server = Ubuntu VM (like AWS EC2)

CI/CD = I’ll explain using Jenkins idea but also mention GitHub Actions briefly.

1️.Repository Setup (GitHub)

Step 1: Create repo on GitHub
          Go to GitHub → New Repository.
          Give it a name, e.g. my-fullstack-app.
          Don’t worry about README/license right now (optional).
          Click Create repository.

Step 2: Connect your local project

    In your project folder (where frontend & backend folders exist):
    
    git init
    git add .
    git commit -m "Initial commit"
    git branch -M main
    git remote add origin https://github.com/your-username/my-fullstack-app.git
    git push -u origin main
    
    
    Now your complete code is on GitHub.

<img width="1919" height="900" alt="Screenshot 2025-11-28 172345" src="https://github.com/user-attachments/assets/2b15c378-58e9-4f44-a795-c51bd6642716" />

2️.Containerization (Dockerizing frontend & backend)

    You want:
    
    One Dockerfile for frontend
    
    One Dockerfile for backend

Step 3: Dockerfile for backend (Node.js)

    In backend/ directory create Dockerfile:



Step 4: Dockerfile for frontend (React example)

    In frontend/ directory:
    
    If you’re serving React using a simple Node server:

<img width="1402" height="713" alt="Screenshot 2025-11-28 173229" src="https://github.com/user-attachments/assets/2f8f18b4-79df-4d84-8bf2-2ab4d0151e0f" />


(There are other patterns like using Nginx, but this is simple.)

3️. Build & Push Images to Docker Hub
Step 5: Log into Docker Hub
docker login
# enter your Docker Hub username & password

Step 6: Build images

    From your project root, run:
    
    # Backend image
    cd backend
    docker build -t yourdockerhubusername/myapp-backend:latest .
    
    # Frontend image
    cd ../frontend
    docker build -t yourdockerhubusername/myapp-frontend:latest .
    
    Step 7: Push images to Docker Hub
    docker push yourdockerhubusername/myapp-backend:latest
    docker push yourdockerhubusername/myapp-frontend:latest


Now your images are stored on Docker Hub, ready to be used anywhere.

4️.Ubuntu VM Setup (on AWS / Azure / etc.)
Step 8: Create an Ubuntu VM

    On AWS as an example:
    
    Go to EC2 → Launch Instance.
    
    Choose Ubuntu image.
    
    Choose a small instance (e.g. t2.micro).
    
    Allow port 22 (SSH) and port 80 (HTTP) in security group.
    
    Launch and download your key pair.

Step 9: SSH into the VM

    From your local machine:
    
    ssh -i your-key.pem ubuntu@your-ec2-public-ip

<img width="1919" height="864" alt="Screenshot 2025-11-28 172051" src="https://github.com/user-attachments/assets/7142bf23-7ab4-4db2-857e-ce713236b1a6" />

5️. Install Docker & Docker Compose on VM
Step 10: Install Docker

On the VM:

    sudo apt update
    sudo apt install -y docker.io
    sudo systemctl enable docker
    sudo systemctl start docker
    
    Step 11: Install Docker Compose plugin (simple way)
    sudo apt install -y docker-compose-plugin
<img width="1919" height="939" alt="image" src="https://github.com/user-attachments/assets/30f3dc4b-e67c-4918-8ab0-76f65e0c8f10" />


Now you can use:

docker compose version

6️. Database Setup (MongoDB)

    Easiest for Docker-based app is: use MongoDB as a Docker container in docker-compose.yml.

7️. Docker Compose on the VM
Step 12: Create project folder on VM
      mkdir myapp
      cd myapp

Step 13: Create docker-compose.yml

Create a file called docker-compose.yml:




    Runs backend container → talks to Mongo using the hostname mongo.
    
    Runs frontend container → accessible on port 80 of your VM.
    
    Runs MongoDB in a container with persistent data.

Step 14: Start the app

On the VM:

    docker compose pull   # pulls latest images from Docker Hub
    docker compose up -d  # runs in background


Check containers:

docker ps


Open browser: http://<your-ec2-public-ip> → you should see your frontend, which talks to backend & DB.

8️. CI/CD Pipeline – Overview (What should happen automatically?)

You want this flow:

You push code to GitHub.

CI/CD tool (Jenkins ):

    The idea is the same, just the tool is different:
    
    Install Jenkins on a server (could be same or separate VM).
    
    Install:
    
    Git plugin
    
    Docker plugin
    
    SSH plugin (or use ssh commands in pipeline).
    
    In Jenkins:
    
    Add GitHub credentials (if private repo).
    
    Add Docker Hub credentials.
    
    Add SSH credentials for VM.
    
    Create a Pipeline job that uses a Jenkinsfile in your repo.
    That Jenkinsfile will:
    
    git checkout code
    
    docker build images
    
    docker push images
    
    ssh into VM and run:
    
    cd myapp
    docker compose pull
    docker compose up -d
<img width="1899" height="964" alt="Screenshot 2025-11-28 172518" src="https://github.com/user-attachments/assets/370aeabc-5086-4589-83b6-2133d9ed5849" />

<img width="1919" height="965" alt="Screenshot 2025-11-28 172613" src="https://github.com/user-attachments/assets/b03e4fd7-4b86-4b16-9eff-ff299f5efc40" />

Set up a GitHub webhook pointing to Jenkins so that every push triggers the pipeline.

    Concept is 100% same as GitHub Actions; only UI and config style changes.
    
    Recap in Plain Human Language
    
    You put your code on GitHub.
    
    You wrap your frontend and backend in Docker images.
    
    You store these images on Docker Hub.
    
    You rent a Linux machine on the cloud (VM) and install Docker + Docker Compose.
    
    You write a docker-compose.yml that knows how to run:
    
    frontend container
    
    backend container
    
    MongoDB container
    
    You use a CI/CD tool (GitHub Actions or Jenkins) that:
    
    Watches your GitHub repo.
    
    Whenever you push:
    
    Builds new Docker images.
    
    Uploads them to Docker Hub.
    
    Logs into your VM.
    
    Updates the running containers.
<img width="1915" height="965" alt="Screenshot 2025-11-28 172232" src="https://github.com/user-attachments/assets/05c07117-496e-4ca0-bad2-9f402e6f2630" />

End result:
👉 You just write code and git push.
👉 The rest (build, push, deploy) happens automatically.
