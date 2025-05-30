# 🐳 Simple Dockerized Web App with Hostname and Git Commit Display

This guide walks you through building a **simple Python Flask app** that displays:
- The **hostname** of the machine/container it runs on.
- The **Git commit hash** of the code it was built from.

Useful for tracking deployments across multiple VMs.

---

## 🧾 Requirements

- Python 3 (in Docker image)
- Docker installed on your system
- Git initialized in your project folder

---

## 📁 Project Structure

```
simple-app/
├── app.py
├── Dockerfile
├── requirements.txt
└── .git/ (optional but needed for commit hash)
```

---

## 🧠 `app.py`

```python
from flask import Flask
import socket
import os

app = Flask(__name__)

@app.route("/")
def index():
    hostname = socket.gethostname()
    commit = os.getenv("GIT_COMMIT", "Unknown")
    return f"""
    <h1>Simple Web App</h1>
    <p><strong>Hostname:</strong> {hostname}</p>
    <p><strong>Git Commit:</strong> {commit}</p>
    """
if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

---

## 📦 `requirements.txt`

```
flask
```

---

## 🐳 `Dockerfile`

```Dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY . .

ARG GIT_COMMIT=unknown
ENV GIT_COMMIT=$GIT_COMMIT

RUN pip install --no-cache-dir -r requirements.txt

EXPOSE 5000

CMD ["python", "app.py"]
```

---

## 🛠️ Build and Run

### 1. Initialize Git

```bash
git init
git add .
git commit -m "Initial commit"
```

### 2. Build Docker Image with Git Commit Hash

```bash
GIT_COMMIT=$(git rev-parse --short HEAD)
docker build --build-arg GIT_COMMIT=$GIT_COMMIT -t simple-web-app .
```

### 3. Run the Docker Container

```bash
docker run -d --name app1 -p 5000:5000 simple-web-app
```

### 4. Test

Visit in browser:

```
http://<your-vm-ip>:5000
```

You should see the hostname (usually container ID) and the commit hash.

---

## ✅ Output Example

```
Simple Web App
Hostname: 6a87477a8027
Git Commit: abc1234
```

---

## 🧼 Clean Up

```bash
docker rm -f app1
docker rmi simple-web-app
```

---

## 🎉 Done

You now have a lightweight, Dockerized app that helps you track deployments easily across different environments.
