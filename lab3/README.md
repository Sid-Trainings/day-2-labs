## 📦 Step 3: Create Dockerfile

### 3.1 Create File
```bash
nano Dockerfile
```

### 3.2 Paste Below Content
```Dockerfile
# Use Python base image
FROM python:3.9-slim

# Set work directory
WORKDIR /app

# Copy files
COPY . /app

# Install dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Expose port
EXPOSE 5000

# Run the app
CMD ["python", "app.py"]
```

Save and exit.

---

## 🛠️ Step 4: Build Docker Image
```bash
docker build -t flask-demo-app .
```

---

## 🚀 Step 5: Run Flask App Container
```bash
docker run -d -p 5000:5000 flask-demo-app
```

Access the app in browser:
```
http://localhost:5000
```

---

## ✅ Verification
If the app loads with your styled HTML page and sections (Welcome, Docker Ready, etc.), you’ve successfully containerized and deployed it locally.

---

## 🚀 What’s Next?
In Lab 4, you'll push this Docker image to Docker Hub.