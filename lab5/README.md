# Lab 5: Jenkins Pipeline for Local CI/CD

## 🧠 Objective
By the end of this lab, you will be able to:
- Create a Jenkins Pipeline for building, testing, and deploying a Dockerized app
- Trigger the pipeline using GitHub code changes
- Deploy locally using Docker

---

## 🔧 Prerequisites
- Jenkins is up and running (from Lab 2)
- Flask app is built and pushed to GitHub (Lab 3)
- Docker installed on the Jenkins host

---

## 📁 Step 1: Prepare GitHub Repository

### 1.1 Fork or clone the Flask app repository (if needed)
```bash
git clone https://github.com/Sid-Trainings/flask-sample-webapp.git
cd flask-sample-webapp
```

### 1.2 Push it to your own GitHub repo (once forked)
```bash
git remote rename origin upstream

git remote add origin https://github.com/<your-github-username>/flask-sample-webapp.git

git push -u origin main
```

---

## 🔐 Step 2: Configure GitHub Credentials in Jenkins

1. Go to **Manage Jenkins > Credentials**
2. Under **(global)**, click **Add Credentials**
3. Choose **Username and Password**
4. Add your GitHub username and a **Personal Access Token** (not password)
5. Save it with an ID like `github-creds`

---

## 🔄 Step 3: Create Jenkins Pipeline Job

1. Go to Jenkins Dashboard > **New Item**
2. Enter job name, select **Pipeline**, click OK
3. Scroll to **Pipeline section**
4. Choose **Pipeline script from SCM**
5. Select **Git**, and enter your GitHub repo URL
6. Add credentials if prompted
7. Set the branch (usually `main`)
8. Save

---

## 📝 Step 4: Add Jenkinsfile to Repo

In the root of your Flask repo, create a file called `Jenkinsfile`:

```groovy
pipeline {
  agent any

  stages {
    stage('Clone Repository') {
      steps {
        git url: 'https://github.com/<your-github-username>/flask-sample-webapp.git', branch: 'main'
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
  }
}
```
> Make sure to replace `<your-github-username>` accordingly.

Push this Jenkinsfile to your GitHub repo:
```bash
git add Jenkinsfile

git commit -m "Add Jenkins pipeline"

git push origin main
```

---

## ▶️ Step 5: Run Your Pipeline

1. Go to your Jenkins job
2. Click **Build Now**
3. Watch the console output for each stage

---

## ✅ Verification
- Open browser: `http://<VM-IP>:5000`
- Your app should be live and running from the Jenkins-built container

---

## 🚀 What’s Next?
In Lab 6, you’ll add a stage to push the Docker image to Docker Hub and deploy to Azure App Service.
