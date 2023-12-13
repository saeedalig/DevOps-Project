## Server Setup for Pipeline

#### Jenkins
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


#### Sonarqube
```
docker run -d --name sonar -p 9000:9000 sonarqube:lts-community
```
Access the app on port 9000. `admin' is its username and password by default

  ##### SonarQube integration with Jenkins
  **Step 1. Generate a Token in SonarQube Server**
  Open SonarQube server - Go to Administration -> click on Security -> Users > Click on Tokens -> Generate token with some name -> Copy the token. It will be used in Jenkins for Sonar authentication.


  
  




#### Docker
```
sudo apt-get update
sudo apt-get install docker.io -y
sudo usermod -aG docker $USER 
newgrp docker
sudo chmod 777 /var/run/docker.sock
```
  **Jenkins Plugins for Docker**
    - Docker
    - Docker Pipeline
    - Docker Commons
    - Docker build


