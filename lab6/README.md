# Lab 6: Jenkins Pipeline – Push to Docker Hub & Deploy to Azure

## 🧠 Objective
By the end of this lab, you will be able to:
- Extend your Jenkins pipeline to push images to Docker Hub
- Deploy the Docker image to Azure App Service

---

## 🔧 Prerequisites
- Docker image built and pushed to local repo (Lab 5)
- Docker Hub account with credentials stored in Jenkins
- Azure CLI installed and Azure account ready

---

## 📦 Step 1: Add Docker Hub Push to Jenkinsfile

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

> Replace `dockerhub-creds` with the actual ID of your Docker Hub credentials in Jenkins.

---

## ☁️ Step 2: Set Up Azure CLI & Service Principal (Manual Step)

### 2.1 Login to Azure (first time only)
```bash
az login
```

### 2.2 Create Resource Group (if not existing)
```bash
az group create --name DevOpsLabRG --location eastus
```

### 2.3 Create Azure App Service Plan & Web App
```bash
az appservice plan create --name DevOpsPlan --resource-group DevOpsLabRG --sku B1 --is-linux

az webapp create --resource-group DevOpsLabRG --plan DevOpsPlan \
  --name flask-webapp-demo --deployment-container-image-name <dockerhub-username>/flask-demo-app:latest
```

---

## 🧪 Step 3: Add Azure Deployment Stage to Jenkinsfile (Optional Automation)

If Azure CLI is installed on the Jenkins host, add:

```groovy
stage('Deploy to Azure') {
  steps {
    sh '''
      az webapp config container set \
        --name flask-webapp-demo \
        --resource-group DevOpsLabRG \
        --docker-custom-image-name $DOCKER_USER/flask-demo-app:latest
    '''
  }
}
```

> You may need to authenticate Azure using a service principal or `az login` manually once.

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
2. Open browser: `https://flask-webapp-demo.azurewebsites.net`
3. Confirm the app is running from Azure App Service

---

## 🎉 You Did It!
You’ve built a full CI/CD pipeline from GitHub to Docker Hub to Azure, fully automated using Jenkins!
