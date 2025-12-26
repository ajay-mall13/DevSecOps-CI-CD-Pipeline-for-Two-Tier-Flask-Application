
# Two-Tier Flask App GitOps Pipeline using Jenkins & Docker
 
##  Flask App with MySQL Docker Setup

This is a simple Flask app that interacts with a MySQL database. The app allows users to submit messages, which are then stored in the database and displayed on the frontend.

## Prerequisites

Before you begin, make sure you have the following installed:

- Docker
- Git (optional, for cloning the repository)

## Setup

1. Clone this repository (if you haven't already):

   ```bash
   git clone https://github.com/your-username/your-repo-name.git
   ```

2. Navigate to the project directory:

   ```bash
   cd your-repo-name
   ```

3. Create a `.env` file in the project directory to store your MySQL environment variables:

   ```bash
   touch .env
   ```

4. Open the `.env` file and add your MySQL configuration:

   ```
   MYSQL_HOST=mysql
   MYSQL_USER=your_username
   MYSQL_PASSWORD=your_password
   MYSQL_DB=your_database
   ```

## Usage

1. Start the containers using Docker Compose:
   ```bash
   sudo apt-get install docker-compose-v2
   ```
 
   ```bash
   docker-compose up -d
   ```

2. Access the Flask app in your web browser:

   - Frontend: http://localhost
   - Backend: http://localhost:5000

3. Create the `messages` table in your MySQL database:

   - Use a MySQL client or tool (e.g., phpMyAdmin) to execute the following SQL commands:
   
     ```sql
     CREATE TABLE messages (
         id INT AUTO_INCREMENT PRIMARY KEY,
         message TEXT
     );
     ```

4. Interact with the app:

   - Visit http://localhost to see the frontend. You can submit new messages using the form.
   - Visit http://localhost:5000/insert_sql to insert a message directly into the `messages` table via an SQL query.

## Cleaning Up

To stop and remove the Docker containers, press `Ctrl+C` in the terminal where the containers are running, or use the following command:

```bash
docker-compose down
```

## To run this two-tier application using  without docker-compose

- First create a docker image from Dockerfile
```bash
docker build -t flaskapp .
```

- Now, make sure that you have created a network using following command
```bash
docker network create twotier
```

- Attach both the containers in the same network, so that they can communicate with each other

i) MySQL container 
```bash
docker run -d \
    --name mysql \
    -v mysql-data:/var/lib/mysql \
    --network=twotier \
    -e MYSQL_DATABASE=mydb \
    -e MYSQL_ROOT_PASSWORD=admin \
    -p 3306:3306 \
    mysql:5.7

```
ii) Backend container
```bash
docker run -d \
    --name flaskapp \
    --network=twotier \
    -e MYSQL_HOST=mysql \
    -e MYSQL_USER=root \
    -e MYSQL_PASSWORD=admin \
    -e MYSQL_DB=mydb \
    -p 5000:5000 \
    flaskapp:latest

```

## Notes

- Make sure to replace placeholders (e.g., `your_username`, `your_password`, `your_database`) with your actual MySQL configuration.

- This is a basic setup for demonstration purposes. In a production environment, you should follow best practices for security and performance.

- Be cautious when executing SQL queries directly. Validate and sanitize user inputs to prevent vulnerabilities like SQL.

- If you encounter issues, check Docker logs and error messages for troubleshooting.

```


```
#  GitOps Pipeline (demo-cicd) using Jenkins 🚀

**Automated Jenkins CI/CD Pipeline triggered by GitHub Webhook**

Jab bhi koi developer `git push` karta hai **GitHub** par, **Jenkins** automatically:

1. `Checkout` — latest code pull karta hai  
2. `Build` — project compile / package karta hai  
3. `Test` — automated tests chalata hai  
4. `Deploy` — target server ya cloud par code release karta hai  

*Docker image build/push yahan cover **nahin** kiya gaya, kyunki aap ne mana kiya hai.*

---

## 🛠️ Prerequisites

| Requirement | Why Needed |
|-------------|------------|
| **Jenkins (LTS)** + *Git* & *Pipeline* plugins | CI/CD engine |
| **GitHub Repo** | Source code aur Webhook trigger |
| **Public URL** (e.g. server IP ya Ngrok) | GitHub ➜ Jenkins webhook delivery |
| Optional: Credentials | Private repo clone ya deploy target ke liye |

---


---
## Set up Jenkins
### ✅ Step 1: Install Java (Required for Jenkins)

```bash
sudo apt update
sudo apt install fontconfig openjdk-21-jre -y
```

🔍 **Check Java Version**
```bash
java -version
```
✅ Output:
```
openjdk version "21.0.3" 2024-04-16
```

---

### ✅ Step 2: Add Jenkins Repository & Install (Stable LTS)

```bash
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key

echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/" \
  | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt-get update
sudo apt-get install jenkins -y
```

---

### ✅ Step 3: Start Jenkins Service

```bash
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

📍 Access Jenkins on: `http://<your-server-ip>:8080`

---




## 🔐 Jenkins Credentials Setup

1. Go to **Jenkins Dashboard > Manage Jenkins > Credentials > Global > Add Credentials**.
2. Choose:
   - **Kind**: `Username with password`
   - **Username**: Your Docker Hub username
   - **Password**: create a personal access token and give this token
   - **ID**: `dockerhub`
3. Click **create**

---





## ⚡ Quick Start or  webhooks set-up

1. **Webhook create karo**   
   GitHub → *Settings* → *Webhooks* →  
   **Payload URL**: `http://<jenkins-ip>:8080/github-webhook/`  
   Content‑Type: `application/json`, Event: **Just the push event**

2. **Jenkins me Pipeline Job banao**  
   - *New Item* → **Pipeline** → Name: `demo-cicd`  
   - *Build Triggers*: ✅ **GitHub hook trigger for GITScm polling**  
   - *Pipeline script from SCM* → Repo URL & branch set karo → `Jenkinsfile` path confirm karo

3. **Jenkinsfile** repo ke root me already present hai. Zaroorat ho to build/test commands modify karo.

4. `git push` karo → Jenkins pipeline turant run hoga.

---

## 📂 Directory Structure

```
.
├── Jenkinsfile
└── src/  (aapka source code yahan)
```

---

---

## 🧩 Troubleshooting Tips

- **Webhook 404**: Jenkins URL galat ya port firewall me blocked?  
- **Build Fail**: Console log padho, dependencies check karo.  
- **Tests Skip**: Test scripts executable & path sahi hai?  
- **Deploy Issues**: Credentials & target server permissions verify karo.

---
---

### 🐳 Successfully pushed the containerized Flask application to Docker Hub  
This image is automatically built and deployed through the Jenkins pipeline.

<img width="100%" alt="Docker Hub Image" src="https://github.com/user-attachments/assets/1916f684-2776-4d7b-8b81-303f09b2ac4c" />



---

### 🔁 End-to-end Jenkins Pipeline  
Triggered by GitHub pushes — automates pull → build → push to Docker Hub → deploy on EC2 using `docker-compose`.

<img width="1625" height="768" alt="Screenshot 2025-08-01 191556" src="https://github.com/user-attachments/assets/765614a1-97e9-4b94-915a-762c44e267eb" />

<img width="1153" height="260" alt="Screenshot 2025-12-26 113945" src="https://github.com/user-attachments/assets/f22b9a9f-09ce-4dcb-b321-9aaf5dcd598a" />

---

### 🗄️ MySQL Database View  
Messages table created inside MySQL container showing stored records.

<img width="100%" alt="MySQL DB Table" src="https://github.com/user-attachments/assets/26d58275-f59c-4c8c-b5c2-b6670fcce373" />

---



---
- **Live Flask web app UI connected to MySQL backend.
User-submitted messages are stored and fetched from the database in real time.

<img width="1920" height="1080" alt="Screenshot (1071)" src="https://github.com/user-attachments/assets/837d4111-43bf-4082-abf5-a3de38299b10" />
---

---

## 🔁 CI/CD Flow Summary

Jab bhi koi developer GitHub par `git push` karta hai:

1. **SCM Trigger**: GitHub webhook se Jenkins ko signal milta hai
2. **Code Checkout**: Jenkins GitHub se latest code clone karta hai
3. **Build**: Flask app ke liye dependencies install karta hai / test run karta hai
4. **Docker Build**: Jenkins Docker image banata hai Flask app ke liye
5. **Push to Docker Hub**: Jenkins Docker image ko DockerHub par push karta hai
6. **Deploy via Docker Compose**: Remote server par Docker Compose file run karke Flask + MySQL containers deploy karta hai

---

## 🙌 Credits

Made with 💛 by *Ajay Mall*.  
Feel free to raise an issue or PR!

---

> **Note:** Ye README short & simple *Hinglish* me likha gaya hai, taaki sabko jaldi samajh aaye. 🙂



