# CI-CD-Pipeline-for-a-Simple-Web-Application-using-Jenkins
For Project

# Automating Deployment with Jenkins CI/CD Pipeline

## Project Overview
This project sets up a Jenkins pipeline to automate the build, test, and deployment process for a simple web application. The pipeline is triggered on every code commit to GitHub, ensuring continuous integration and deployment.

---

## Prerequisites
- An AWS EC2 instance (Ubuntu or Amazon Linux)
- Jenkins installed and running
- GitHub repository with a sample web application
- SSH access to the EC2 instance
- Basic knowledge of Linux commands

---

## Step 1: Set Up Jenkins on AWS EC2
1. Launch an EC2 instance (Ubuntu or Amazon Linux).
2. Connect to the instance via SSH:
   ```bash
   ssh -i your-key.pem ec2-user@your-ec2-ip
   ```
3. Install Java:
   ```bash
   sudo apt update
   sudo apt install openjdk-11-jdk -y
   ```
4. Install Jenkins:
   ```bash
   wget -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc
   echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
   sudo apt update
   sudo apt install jenkins -y
   ```
5. Start and enable Jenkins:
   ```bash
   sudo systemctl start jenkins
   sudo systemctl enable jenkins
   ```
6. Access Jenkins:
   - Open `http://<EC2-Public-IP>:8080` in a browser.
   - Retrieve the initial admin password:
     ```bash
     sudo cat /var/lib/jenkins/secrets/initialAdminPassword
     ```
   - Follow the setup wizard and install the suggested plugins.

---

## Step 2: Configure Git and GitHub Integration
1. Install Git on the EC2 instance:
   ```bash
   sudo apt install git -y
   ```
2. Create a GitHub repository and upload a sample web application.
3. Generate an SSH key for Jenkins:
   ```bash
   sudo su - jenkins
   ssh-keygen -t rsa -b 4096 -C "jenkins@yourdomain.com"
   cat ~/.ssh/id_rsa.pub
   ```
4. Add the public key to GitHub under **Settings → Deploy Keys**.

---

## Step 3: Install and Configure Jenkins Plugins
1. In Jenkins, navigate to **Manage Jenkins → Plugin Manager**.
2. Install the following plugins:
   - **Git Plugin**
   - **Pipeline Plugin**
   - **SSH Pipeline Steps Plugin**

---

## Step 4: Create a Jenkins Pipeline
1. In Jenkins, create a new **Pipeline** project.
2. Use the following pipeline script in the **Pipeline** section:

   ```groovy
   pipeline {
       agent any
       stages {
           stage('Clone Repository') {
               steps {
                   git 'git@github.com:your-username/your-repo.git'
               }
           }
           stage('Build') {
               steps {
                   sh 'echo "Building the application..."'
                   sh 'npm install'  // Modify based on your application
               }
           }
           stage('Test') {
               steps {
                   sh 'echo "Running tests..."'
                   sh 'npm test'  // Modify based on your application
               }
           }
           stage('Deploy') {
               steps {
                   sshagent(['your-ssh-credential-id']) {
                       sh '''
                       ssh ec2-user@your-ec2-instance 'bash -s' <<EOF
                       cd /var/www/html
                       git pull origin main
                       npm install --production
                       pm2 restart all
                       EOF
                       '''
                   }
               }
           }
       }
   }
   ```
3. Save and run the pipeline.

---

## Step 5: Monitor and Debug the Pipeline
1. View the **Build Console Output** for errors.
2. Ensure Jenkins can connect to the EC2 instance using SSH.
3. If required, adjust permissions:
   ```bash
   sudo chmod 600 ~/.ssh/id_rsa
   ```

---

## Outcome
By the end of this project, you will have a fully automated CI/CD pipeline in Jenkins that pulls code from GitHub, builds the application, runs tests, and deploys it to an AWS EC2 instance.

---

