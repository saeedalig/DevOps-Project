# Server Setup for Pipeline

## Jenkins
```
sudo apt install openjdk-17-jdk -y

sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update -y
sudo apt-get install jenkins -y

sudo systemctl enable jenkins
sudo systemctl restart jenkins

# Access on default port 8080

# To get Password
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
![Alt text](images/jenkins.png)


## Sonarqube
```
docker run -d --name sonar -p 9000:9000 sonarqube:lts-community
```
Access the app on port 9000. `admin' is its username and password by default

### SonarQube integration with Jenkins 

**Step 1. Generate a Token on SonarQube Server:**
- -> Open SonarQube server -> Go to Administration
- -> Click on Security
- -> Users
- -> Click on Tokens
- -> Generate token with some name
- -> Copy the token. It will be used in Jenkins for Sonar authentication.

![Alt text](images/sonar-1.png)
![Alt text](images/sonar-2.png)

**Step 2. Setup SonarQube in Jenkin Server:**

First install *SonarQube Scanner Plugin*: Dashboard -> Manage Jenkins -> Plugins -> Available Plugins -> Search

**System**
- -> Go to Manage Jenkins -> System -> Scroll down to SonarQube server Section 
- -> Name: *sonar-server*
- -> Server URL: *http://192.168.1.0:9000*          # Replace URL
- -> Server authentication token: *sonar-token*     # Make sure you have added sonarqube token to jenkins credentials 
- -> Apply and Save

![Alt text](images/sonar-system.png)
![Alt text](images/sonar-token-jenkins.png)

**Tools**
- -> Go to Manage Jenkins -> Tools -> Scroll down to SonarQube server Installation -> Add Sonarqube Server
- -> Name: *sonar-scanner*
- -> Check the box to install automatically -> Select the version you need (optional)
- -> Apply and Save
  
 ![Alt text](images/sonar-tools.png) 

 ## OWASP Dependency-Check
Dashboard -> Manage Jenkins -> Plugins -> Available Plugins -> Search `Dependency-Check` and install.
![Alt text](images/dp-check.png) 

Once installed, Go to Manage Jenkins -> Tools -> Scroll down to Dependency-Check -> Add `Dependency-Check`
- -> Name: *DP-Check*
- -> Check the box to install automatically
- -> Add Installer: *Install from github.com*
![Alt text](images/dp-tools.png)


## Install Trivy to scan the File System and Docker Images to detect vulnerabilities
```
sudo apt-get install wget apt-transport-https gnupg lsb-release -y
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | sudo tee /usr/share/keyrings/trivy.gpg > /dev/null

echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | sudo tee -a /etc/apt/sources.list.d/trivy.list

sudo apt-get update -y
sudo apt-get install trivy -y
```

### Docker
```
sudo apt-get update
sudo apt-get install docker.io -y
sudo usermod -aG docker $USER 
newgrp docker
sudo chmod 777 /var/run/docker.sock
```
  **Jenkins Plugins for Docker**
- Docker
- Docker API
- Docker Pipeline
- Docker Commons Plugin
- docker-build steps

![Alt text](images/docker-plugins.png)

To push the Docker Images to Dockerhub, you will be authenticated. To achieve so, add the Personal Access Token (PAT) of your Dockerhub Acount into Jenkins Credentials
- **Generate Dockerhub PAT:** Go to Dockerhub -> Sign In/Sign Up -> My Account -> Security -> New Access Token

![Alt text](images/dockerhub.png)

- **Jenkins Server:** Dashboard -> Manage Jenkins -> Credetials -> System -> Global Credentials(unrestricted)
   - ***Kind***: Username and Password
   - ***Scope***: Global
   - ***Username***: <your-dockerhub-account>
   - ***Password***: Paste the Copied PAT
   - ***ID***: dockerhub-passwd
   - ***Description***: dockerhub-passwd(optional)

![Alt text](images/dockerhub-jenkins.png)
