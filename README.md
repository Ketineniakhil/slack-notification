# 🚀 CI/CD Pipeline for Node.js with Jenkins, PM2, and Nginx  

This repository provides a **complete CI/CD pipeline** using **Jenkins, PM2, and Nginx** for **automated deployment of a Node.js application** to a **staging server**.

## 📌 Features  
- ✅ **Automated Deployment**: Jenkins fetches the latest code and deploys it automatically.  
- ✅ **PM2 for Process Management**: Ensures the Node.js app stays running.  
- ✅ **Nginx as Reverse Proxy**: Serves the app via a clean URL (`http://your-staging-server`).  
- ✅ **SSH-based Secure Deployment**: Uses private key authentication.  
- ✅ **Scalable & Reproducible**: Can be extended for production environments.  

---

## 🛠 Prerequisites  
Before setting up the CI/CD pipeline, ensure you have the following:  

### **1️⃣ Jenkins Server (Dockerized)**
- **Jenkins installed inside Docker**  
- **Git, Node.js, npm, and SSH access configured**  
- **Required Jenkins plugins**:  
  - [Git Plugin](https://plugins.jenkins.io/git/)  
  - [Pipeline Plugin](https://plugins.jenkins.io/workflow-aggregator/)  

### **2️⃣ Staging Server (Ubuntu 20.04/22.04)**
- **Public IP (e.g., `your ip`)**  
- **Node.js and npm installed**  
- **SSH access enabled**  
- **PM2 installed for process management**  
- **Nginx installed for reverse proxy (optional)**  

---

## 📂 Project Structure  
├── Jenkinsfile # CI/CD pipeline for Jenkins                         
├── package.json # Node.js project configuration                  
├── index.js # Main application entry point                               
├── .gitignore # Files to ignore in Git                         


## 🚀 Step-by-Step Setup Guide 

#### **🔹 Start Jenkins in Docker with SSH Keys**
If you haven't already set up Jenkins inside Docker, use:  
```
docker run -d --name jenkins -p 8080:8080 -p 50000:50000 \
    -v jenkins_home:/var/jenkins_home \
    -v ~/.ssh:/root/.ssh:ro \
    jenkins/jenkins:lts
```
This mounts SSH keys so Jenkins can access the staging server.
Ensure your SSH private key (your pem key) is present inside ~/.ssh.

🔹 Install Required Jenkins Plugins
Go to Jenkins Dashboard → Manage Jenkins → Manage Plugins.
Install:
Git Plugin (for pulling code from GitHub)
Pipeline Plugin (for running the Jenkinsfile)

2️⃣ Set Up the Staging Server
🔹 Connect to the Staging Server
```
ssh -i ~/.ssh/default-ecc.pem ubuntu@YOUR-IP-ADDRESS
```
🔹 Install Node.js and npm
```
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs
```
✅ Verify installation:
```
node -v
npm -v
```

🔹 Install PM2 for Process Management
```
sudo npm install -g pm2
```

✅ Set PM2 to start on reboot:
```
pm2 startup systemd
```
Then copy and run the command shown in the output.

3️⃣ Set Up Nginx as a Reverse Proxy (Optional)
To access your app without specifying a port 

🔹 Install Nginx
```
sudo apt install -y nginx
```
🔹 Configure Nginx
```
sudo nano /etc/nginx/sites-available/default
```
Replace the content with:

nginx
```
server {
    listen 80;
    server_name YOUR IP;

    location / {
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```
🔹 Restart Nginx

```
sudo systemctl restart nginx
````
✅ Deployment Steps
Push your code to GitHub:
```
git add .
git commit -m "Initial CI/CD setup"
git push origin main
```
Trigger the Jenkins pipeline manually or via GitHub webhook.
Jenkins will:
Clone the latest code.
Install dependencies.
Run tests.
Deploy the app to the staging server.
Start the app using PM2.

**OUTPUT OF JENKINS CI/CD**
![git project](https://github.com/user-attachments/assets/c056a3ac-c7a2-41d0-abfa-7c0cb1af44f7)
