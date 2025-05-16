# Lab 4: Push Docker Image to Docker Hub

## 🧠 Objective
By the end of this lab, you will be able to:
- Tag and push a Docker image to Docker Hub
- Verify the image on your Docker Hub profile

---

## 🔧 Prerequisites
- Docker installed and image built (Lab 3)
- Docker Hub account

---
## Docker Hub Account Creation
In your desktop go to following address:
> https://hub.docker.com/
- Click on Sign Up
- Select **Personal Tab**
- Enter your email address
- Put your desired Username
- Enter your password
- Click on Sign Up

## 👤 Step 1: Login to Docker Hub
```bash
docker login -u <your_username>
```
Enter your Docker Hub **username and password** when prompted.

---

## 🏷️ Step 2: Tag Your Docker Image
Replace `<your_dockerhub_username>` with your actual Docker Hub username:
```bash
docker tag flask-demo-app <your_dockerhub_username>/flask-demo-app:v1
```

Example:
```bash
docker tag flask-demo-app sidtrainings/flask-demo-app:v1
```

---

## ⏫ Step 3: Push the Image to Docker Hub
```bash
docker push <your_dockerhub_username>/flask-demo-app:v1
```

---

## ✅ Step 4: Verify on Docker Hub
- Go to [https://hub.docker.com](https://hub.docker.com)
- Login to your account
- Navigate to **Repositories** and confirm your image `flask-demo-app:v1` is listed

---

## 🚀 What’s Next?
In Lab 5, you will write a Jenkins pipeline to automate image build and deployment.
