# 01-docker-flask-jenkins
“A beginner-friendly CI/CD project using Flask, Docker, and Jenkins to demonstrate DevOps fundamentals.
## 🧱 Tech Stack
- Python (Flask)
- Docker
- Jenkins (CI/CD)
- DockerHub (image registry)

---

## 🏗️ Architecture
![Architecture Diagram](docs/architecture.png)

1. Developer pushes code to GitHub  
2. Jenkins pulls code → builds Docker image  
3. Jenkins pushes image to DockerHub  
4. (Optional) Jenkins deploys container to EC2 or Kubernetes  

---

## ⚙️ Run Locally

```bash
git clone https://github.com/tambe-devops/01-docker-flask-jenkins.git
cd 01-docker-flask-jenkins
docker build -t flask-jenkins:latest .
docker run -p 5000:5000 flask-jenkins:latest
Visit 👉 http://localhost:5000
