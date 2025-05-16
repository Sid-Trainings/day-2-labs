# Lab 6: Jenkins Pipeline – Push to Docker Hub 
## 🧠 Objective
By the end of this lab, you will be able to:
- Extend your Jenkins pipeline to push images to Docker Hub


---

## 🔧 Prerequisites
- Docker image built and tested locally (Lab 5)
- Docker Hub account (create one at https://hub.docker.com)
- Azure account (create one at https://portal.azure.com)
- Jenkins is running with Docker CLI and Azure CLI installed (see setup steps below)

---

## 🛠️ Step 0: Prepare Jenkins with Docker & Azure CLI (Run as root)

### 0.1 Access Jenkins container as root
```bash
docker exec -u 0 -it jenkins bash
```

### 0.2 Install Docker CLI inside Jenkins container
```bash
apt update
apt install -y docker.io
```

### 0.3 Install Azure CLI
```bash
apt install -y ca-certificates curl apt-transport-https lsb-release gnupg
curl -sL https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
install -o root -g root -m 644 microsoft.gpg /etc/apt/trusted.gpg.d/
rm microsoft.gpg
AZ_REPO=$(lsb_release -cs)
echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ $AZ_REPO main" > /etc/apt/sources.list.d/azure-cli.list
apt update
apt install -y azure-cli
```

---

## 🔐 Step 1: Store Docker Hub Credentials in Jenkins

### 1.1 Generate a Personal Access Token (PAT) on Docker Hub (optional)
If using PATs instead of passwords: https://hub.docker.com/settings/security

### 1.2 Add Credentials in Jenkins
1. Go to Jenkins → **Manage Jenkins** → **Credentials** → **(global)** → **Add Credentials**
2. Choose **Username with password**
3. Enter your Docker Hub **username** and **password or PAT**
4. Set **ID** as `dockerhub-creds` and click **Save**

---

## 📦 Step 2: Add Docker Hub Push to Jenkinsfile

Update your `Jenkinsfile` with an additional stage:

```groovy
stage('Push to Docker Hub') {
  steps {
    withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
      sh '''
        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
        docker tag flask-demo-app $DOCKER_USER/flask-demo-app:latest
        docker push $DOCKER_USER/flask-demo-app:latest
      '''
    }
  }
}
```

> Replace `dockerhub-creds` with your actual Jenkins credential ID if different.

---
## 📦Step 2.a: Complete Jenkins File Syntax

If you need complete ```Jenkins``` file here is the syntax:
```groovy
pipeline {
  agent any

  stages {
    stage('Clone Repository') {
      steps {
        git url: 'https://github.com/<your_username>/flask-sample-webapp.git', branch: 'main'
      }
    }

    stage('Build Docker Image') {
      steps {
        sh 'docker build -t flask-demo-app .'
      }
    }

    stage('Run Container') {
      steps {
        sh 'docker stop flask-container || true'
        sh 'docker rm flask-container || true'
        sh 'docker run -d --name flask-container -p 5000:5000 flask-demo-app'
      }
    }

    stage('Push to Docker Hub') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
          sh '''
            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
            docker tag flask-demo-app $DOCKER_USER/flask-demo-app:latest
            docker push $DOCKER_USER/flask-demo-app:latest
          '''
        }
      }
    }
  }
}
```
---
## Step 3: Build your pipeline
1. Go to your Jenkins Server at ```http://<your_vm_ip>:8080```
2. Go to your Job
3. Click on ***Build Now***

### Step 3.1: Verify your Image has been published to DockerHub

1. Go to DockerHub
2. Browse your repositories
3. Verify that the new version with `latest` tag is added

---
## 🚀 What’s Next?
In [Lab 7](./lab7/lab7.md), you will:
- Automatically deploy code to Azure App Service using GitHub integration
- Trigger a Jenkins job via webhook to build and push a Docker image to Docker Hub