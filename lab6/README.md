# Lab 6: Jenkins Pipeline – Push to Docker Hub & Deploy to Azure

## 🧠 Objective
By the end of this lab, you will be able to:
- Extend your Jenkins pipeline to push images to Docker Hub
- Deploy the Docker image to Azure App Service

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

## ☁️ Step 3: Set Up Azure for Deployment

### 3.1 Login to Azure (manual, first time only)
```bash
az login
```

### 3.2 Create Resource Group
```bash
az group create --name DevOpsLabRG --location eastus
```

### 3.3 Create App Service Plan & Web App

We’ll now deploy our Dockerized Flask app to Azure App Service using a Linux-based plan.

```bash
# Variables (update with your Docker Hub username)
RESOURCE_GROUP="DevOpsLabRG"
PLAN_NAME="DevOpsPlan"
APP_NAME="flask-webapp-demo" #change this to something unique
LOCATION="Central India"
DOCKER_IMAGE="<dockerhub-username>/flask-demo-app:latest"

# Step 1: Create App Service Plan (Linux + B1)
az appservice plan create \
  --name $PLAN_NAME \
  --resource-group $RESOURCE_GROUP \
  --location "$LOCATION" \
  --sku B1 \
  --is-linux

# Step 2: Create Web App with container image
az webapp create \
  --resource-group $RESOURCE_GROUP \
  --plan $PLAN_NAME \
  --name $APP_NAME \
  --deployment-container-image-name $DOCKER_IMAGE

# Step 3 (Optional): Disable App Service Storage (recommended for containers)
az webapp config appsettings set \
  --resource-group $RESOURCE_GROUP \
  --name $APP_NAME \
  --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```
>💡 Replace <dockerhub-username> with your actual Docker Hub handle (e.g., skarkhanis96).
---

## 🧪 Step 4: Add Azure Deployment Stage to Jenkinsfile (Optional Automation)

If Azure CLI is available on the Jenkins host/container, you can automate deployment:

```groovy
stage('Deploy to Azure') {
  steps {
    withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
      sh '''
        az webapp config container set \
          --name flask-webapp-demo \
          --resource-group DevOpsLabRG \
          --docker-custom-image-name $DOCKER_USER/flask-demo-app:latest
      '''
    }
  }
}

```

> ⚠️ You must have already logged in to Azure or use a service principal for headless access.

---

## ✅ Final Jenkinsfile Structure Overview
Your Jenkinsfile should now include:
- Clone repo
- Build image
- Run locally
- Push to Docker Hub
- (Optional) Deploy to Azure

---

## 🖥️ Verification
1. Go to Docker Hub and verify your image was pushed
2. Visit your Azure app URL:
```
https://flask-webapp-demo.azurewebsites.net
```
3. Your Flask app should load successfully

---

## 🎉 You Did It!
You’ve now built a full DevOps CI/CD pipeline — from GitHub to Docker Hub to Azure App Service, fully automated via Jenkins!
