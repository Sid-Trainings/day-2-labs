# Lab 2: Setting Up Jenkins using Docker

## 🧠 Objective
By the end of this lab, you will be able to:
- Set up a Jenkins server using Docker
- Access Jenkins from the browser
- Prepare Jenkins for CI/CD with plugins

---

## 🔧 Prerequisites
- Docker must be installed (from Lab 1)
- Internet connection

>⚠️ Note: Use the same CentOS/RHEL VM from Lab 1 where Docker is already installed. No need to create a new VM.

---

## 🖥️ Step 1: Pull Jenkins Docker Image
```bash
docker pull jenkins/jenkins:lts
```

---

## ⚙️ Step 2: Create a Docker Volume for Jenkins Data
```bash
docker volume create jenkins_home
```

---

## 🚀 Step 3: Run Jenkins Container
```bash
docker run -d \
  --name jenkins \
  -p 8080:8080 -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  jenkins/jenkins:lts
```

> The above command runs Jenkins in detached mode and maps ports.

---

## 🌐 Step 4: Access Jenkins in Browser
Open your browser and go to:
```
http://localhost:8080
```
>💡 To access Jenkins from your host machine’s browser, find your VM’s IP using:
```ip addr``` or ```ip a```
Look for the IP under eth0 or similar interface.
---

## 🔑 Step 5: Unlock Jenkins
To retrieve the initial admin password:
```bash
docker exec -it jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

Paste this password in the browser prompt to unlock Jenkins.

---

## 🧩 Step 6: Install Suggested Plugins
- Choose "Install suggested plugins"
- Wait until installation completes

---

## 👤 Step 7: Create Admin User
Fill in:
- Username
- Password
- Full Name
- Email Address

Click **Save and Continue**.

---

## ✅ Step 8: Verify Jenkins is Ready
You should now see the Jenkins dashboard.

---

## 🚀 What’s Next?
In the next lab, you’ll:
- Clone a GitHub repository
- Containerize the application
- Push it to Docker Hub
- Automate this with a Jenkins pipeline
