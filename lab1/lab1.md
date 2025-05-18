# Lab 1: Installing Docker on CentOS/RHEL

## 🧠 Objective
By the end of this lab, you will be able to:
- Install Docker on a CentOS/RHEL VM
- Verify Docker installation
- Run your first Docker container
- Configure Git and GitHub for upcoming labs

---

## 🔧 Prerequisites
- VM running CentOS/RHEL (via VMware Workstation)
- Sudo/root access on the VM
- Internet connection

---

## 📦 Step 1: Install Git and Configure GitHub

### 1.1 Install Git
```bash
sudo yum install -y git tree nano
```

### 1.2 Configure Git (use your GitHub credentials)
```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

### 1.3 Verify Configuration
```bash
git config --list
```

---

## 🖥️ Step 2: Install Docker - Manual Method

### 2.1 Remove Older Versions (if any)
```bash
sudo yum remove docker \
                docker-client \
                docker-client-latest \
                docker-common \
                docker-latest \
                docker-latest-logrotate \
                docker-logrotate \
                docker-engine
```

### 2.2 Set up the Docker repository (Redhat/CentOS)
```bash
sudo yum install -y yum-utils
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/rhel/docker-ce.repo
```
### 2.2(a) Set up the Docker repository (Debian)
```bash
sudo apt update
sudo apt install -y apt-transport-https ca-certificates curl gnupg lsb-release

# Add Docker’s official GPG key
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Add Docker’s stable repository
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] \
  https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Update and install Docker
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io

```

### 2.3 Install Docker Engine
```bash
sudo yum install -y docker-ce docker-ce-cli containerd.io
```

### 2.4 Start and Enable Docker
```bash
sudo systemctl start docker
sudo systemctl enable docker
```

### 2.5 Add your user to the docker group
```bash
sudo usermod -aG docker $USER
```
> You must logout and log back in for this to take effect.

---

## ⚙️ Step 3: Install Docker - Script Method

If you prefer automation, use this script:

### 3.1 Create Script
```bash
nano install_docker.sh
```

### 3.2 Paste Below Code
```bash
#!/bin/bash
sudo yum remove -y docker* 
sudo yum install -y yum-utils
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/rhel/docker-ce.repo
sudo yum install -y docker-ce docker-ce-cli containerd.io
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker $USER
```
>**Save & Exit:** Press **Ctrl+O** --> Press **Enter** --> **Ctrl+X**
### 3.3 Run the Script
```bash
chmod +x install_docker.sh
./install_docker.sh
```
> Logout and log back in to apply group changes.

---

## ✅ Step 4: Verify Docker Installation

### 4.1 Check Docker Version
```bash
docker --version
```

### 4.2 Run Hello World Container
```bash
docker run hello-world
```

If you see a success message, Docker is installed correctly.

---

## 🚀 What’s Next?
Proceed to [Lab 2](../lab2/lab2.md) Setting up Jenkins using Docker.
