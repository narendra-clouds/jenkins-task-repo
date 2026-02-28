# jenkins-task-repo

## MCQs Questions

### 1. What is Jenkins mainly used for?
- A) Server Monitoring  
- B) Continuous Integration and Continuous Delivery  
- C) Database Management  
- D) Container Orchestration  

**Answer:** B) Continuous Integration and Continuous Delivery  

---

### 2. Which type of job allows you to define build steps using code in Jenkins?
- A) Freestyle Project  
- B) Pipeline Project  
- C) Multi-Configuration Project  
- D) External Job  

**Answer:** B) Pipeline Project  

---

### 3. Which file is used to define a pipeline in Jenkins?
- A) pipeline.yaml  
- B) dockerfile  
- C) Jenkinsfile  
- D) build.gradle  

**Answer:** C) Jenkinsfile  

---

### 4. What is the purpose of a Jenkins Agent (Node)?
- A) To store source code  
- B) To execute jobs assigned by the Jenkins controller  
- C) To manage plugins  
- D) To configure webhooks  

**Answer:** B) To execute jobs assigned by the Jenkins controller  

---

### 5. Which plugin is required to connect Jenkins with GitHub?
- A) Docker Plugin  
- B) Git Plugin  
- C) Kubernetes Plugin  
- D) Maven Plugin  

**Answer:** B) Git Plugin  

---

### 6. What is the purpose of a Webhook in Jenkins CI/CD?
- A) To install plugins  
- B) To trigger build automatically on code push  
- C) To secure Jenkins server  
- D) To restart Jenkins  

**Answer:** B) To trigger build automatically on code push  

---

### 7. Which command is used inside Jenkins Pipeline to execute shell commands?
- A) bash  
- B) cmd  
- C) sh  
- D) run  

**Answer:** C) sh  

---

### 8. What is the purpose of post block in Jenkins Pipeline?
- A) Define environment variables  
- B) Execute steps after pipeline stages  
- C) Define agents  
- D) Install plugins  

**Answer:** B) Execute steps after pipeline stages  

---

### 9. What is the use of sshagent in Jenkins Pipeline?
- A) Install SSH on server  
- B) Store SSH keys  
- C) Use stored SSH credentials during execution  
- D) Restart SSH service  

**Answer:** C) Use stored SSH credentials during execution  

---

### 10. What happens if a stage fails in Jenkins Pipeline (by default)?
- A) The pipeline continues to next stage  
- B) The pipeline stops execution  
- C) Jenkins restarts automatically  
- D) All stages are skipped but marked successful  

**Answer:** B) The pipeline stops execution  

----






# Scenario Based questions

# 🚀 FitLife Gym – CI/CD Deployment using Jenkins

## 📌 Project Overview

This project demonstrates a complete **CI/CD pipeline setup** using Jenkins to automatically deploy a static HTML website to a target Linux server using SSH.

The goal of this project is to simulate a real-world DevOps workflow where:

* Developers push code to GitHub
* Jenkins automatically triggers a build
* Website gets deployed to a remote server
* Nginx serves the updated website

---

# 🏗️ Project Architecture

```
Developer → GitHub → Webhook → Jenkins → SSH → Target Server → Nginx
```

---

# 🛠️ Technologies Used

* Jenkins
* GitHub
* Linux (Ubuntu)
* Nginx
* SSH
* Git

---

# 📂 Project Structure

```
gym-static-website/
│── index.html
│── Jenkinsfile
│── README.md
```

---

# 🖥️ Task 1 – Infrastructure Setup

## 1️⃣ Jenkins Server Setup

* Installed Java
* Installed Jenkins
* Installed required plugins:

  * Git Plugin
  * Pipeline Plugin
  * SSH Agent Plugin
  * GitHub Integration Plugin
* Configured SSH credentials

📸 **Attach Screenshot Here:**

* Topic Name: `<img width="1884" height="631" alt="Jenkins Dashboard" src="https://github.com/user-attachments/assets/5579deef-a752-436e-8938-ef4c74745228" />`
* Topic Name: `<img width="1865" height="810" alt="Installed Plugins" src="https://github.com/user-attachments/assets/ce54de22-7311-494d-bf59-166fc740a591" />`
* Topic Name: `<img width="1848" height="900" alt="SSH Credentials Configuration" src="https://github.com/user-attachments/assets/56758323-11f4-4203-87f9-a7bd41dd28f3" />`

---

## 2️⃣ Target Server Setup

* Installed Nginx
* Enabled and started Nginx
* Opened port 80 in security group
* Verified website accessibility in browser

📸 **Attach Screenshot Here:**

* Topic Name: `<img width="1651" height="794" alt="Nginx Default Page" src="https://github.com/user-attachments/assets/56d82b25-539b-4c7c-80c4-2969233c79e1" />`
* Topic Name: `<img width="1842" height="385" alt="Security Group Port 80 Open" src="https://github.com/user-attachments/assets/0518cfe4-c5cc-44a5-8964-bc97f0257992" />`

---

# 📦 Task 2 – GitHub Repository Setup

* Created public repository: `gym-static-website`
* Added static HTML code
* Pushed to main branch

📸 **Attach Screenshot Here:**

* Topic Name: `GitHub Repository`
* Topic Name: `Initial Commit`

---

# 🔄 Task 3 – Website Modification

Changed:

```
Hello from FitLife Gym
```

To:

```
Hi from FitLife Gym
```

Committed and pushed changes to GitHub.

📸 **Attach Screenshot Here:**

* Topic Name: `Updated HTML Code`
* Topic Name: `GitHub Commit After Change`

---

# 🔗 Task 4 – GitHub Webhook Configuration

Configured webhook to trigger Jenkins automatically on push.

Webhook URL:

```
http://<JENKINS_IP>:8080/github-webhook/
```

Selected:

* Application/json
* Just the push event

📸 **Attach Screenshot Here:**

* Topic Name: `GitHub Webhook Settings`
* Topic Name: `Recent Webhook Deliveries`

---

# ⚙️ Task 5 – Jenkins Pipeline

Created a Pipeline job using a `Jenkinsfile`.

Pipeline Stages:

1. Clone Repository
2. Deploy to Target Server
3. Restart Nginx
4. Post Success/Failure Messages

---

## 📝 Jenkinsfile

```groovy
pipeline {
    agent any

    environment {
        SERVER_IP = 'YOUR_TARGET_IP'
        SSH_CREDENTIAL = 'gym-key'
        REPO_URL = 'https://github.com/your-username/gym-static-website.git'
        BRANCH = 'main'
        REMOTE_USER = 'ubuntu'
        REMOTE_PATH = '/var/www/html'
    }

    stages {

        stage('Clone Repository') {
            steps {
                git branch: "${BRANCH}", url: "${REPO_URL}"
            }
        }

        stage('Deploy to Target Server') {
            steps {
                sshagent([SSH_CREDENTIAL]) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${SERVER_IP} 'mkdir -p ${REMOTE_PATH}'
                    scp -o StrictHostKeyChecking=no -r * ${REMOTE_USER}@${SERVER_IP}:${REMOTE_PATH}
                    """
                }
            }
        }

        stage('Restart Nginx') {
            steps {
                sshagent([SSH_CREDENTIAL]) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${SERVER_IP} '
                    sudo systemctl restart nginx
                    '
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful'
        }
        failure {
            echo 'Deployment Failed'
        }
    }
}
```

📸 **Attach Screenshot Here:**

* Topic Name: `Pipeline Job Configuration`
* Topic Name: `Pipeline Successful Build`
* Topic Name: `Console Output`
* Topic Name: `Deployed Website in Browser`

---

# 🐞 Issue Faced & Fix

## ❌ Problem:

Permission denied while deploying to `/var/www/html`.

## 💡 Root Cause:

Ubuntu user did not have write permission on `/var/www/html`.

## ✅ Solution:

Changed ownership:

```bash
sudo chown -R ubuntu:ubuntu /var/www/html
```

After that deployment worked successfully.

📸 **Attach Screenshot Here:**

* Topic Name: `Permission Denied Error`
* Topic Name: `Successful Deployment After Fix`

---

# 🔥 Key Learnings

* How to configure Jenkins CI/CD
* How GitHub Webhooks trigger pipelines
* How to use SSH Agent in Jenkins
* How to troubleshoot permission issues
* How to automate deployment to Nginx server

---

# 🎯 Interview Explanation (How I Describe This Project)

> I implemented a complete CI/CD pipeline using Jenkins to automatically deploy a static website hosted on GitHub to a remote Linux server. I configured GitHub webhooks for automatic trigger, used SSH credentials for secure deployment, and automated Nginx restart. I also troubleshooted permission-related deployment issues.

---

# 📌 Future Improvements

* Use AWS EC2 instead of local servers
* Add Docker containerization
* Use Nginx reverse proxy
* Implement HTTPS using SSL
* Add Blue-Green Deployment strategy

---

# 👨‍💻 Author

**Narendra Deshmukh**
Cloud & DevOps Learner ☁️

---

If you want, I can now:

* Make a 🔥 advanced README version (with badges & icons)
* Or make a professional LinkedIn project description for this
* Or review your GitHub repo like a recruiter 😎


