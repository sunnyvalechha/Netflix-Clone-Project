# Netflix-Clone-Project

![image](https://github.com/sunnyvalechha/Netflix-Clone-Project/assets/59471885/324c5356-24a0-4179-89a2-f806ec752736)

Github URL: https://github.com/N4si/DevSecOps-Project

This Project is devided in 3 parts. Dev, Sec, Ops

**Part 1 - Dev**
    
    sudo apt update -y
    git clone https://github.com/N4si/DevSecOps-Project.git

Go Inside the directory

**Install Docker**

    sudo apt-get update
    sudo apt-get install docker.io -y
    sudo usermod -aG docker $USER  # Replace with your system's username, e.g., 'ubuntu'
    newgrp docker
    sudo chmod 777 /var/run/docker.sock

    sudo reboot

Go to The movie database (TMDB)

    https://www.themoviedb.org/

Get the API Token 

![image](https://github.com/sunnyvalechha/Netflix-Clone-Project/assets/59471885/fda4671a-729c-4faa-bdf7-961640abf8fa)

    docker build --build-arg TMDB_V3_API_KEY=<API KEY FROM TMDB> -t netflix .

    docker run -d -p 8081:80 <docker image id>

Run the docker app with Aws-Public-IP:8081

![image](https://github.com/sunnyvalechha/Netflix-Clone-Project/assets/59471885/5d0d3289-2a2f-4272-a9d0-e97c617c5cdc)

**Part 2 - Security**

For Security we use 2 tools SonarQube and Trivy

SonarQube

        docker run -d --name sonar -p 9000:9000 sonarqube:lts-community

        http://52.91.124.230:9000/

        Username / Password: admin
        New Password: pass

Trivy
    
        sudo apt-get install wget apt-transport-https gnupg lsb-release
        wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
        echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list
        sudo apt-get update
        sudo apt-get install trivy

To scan the file system

        trivy fs .

To scan the image using trivy run below command, Image is netflix image, If found any High Vulnerability so the Developer should make the changes in image

        trivy image a67754fda871

**Install Jenkins on the EC2 instance to automate deployment:**

        #Java
        sudo apt update
        sudo apt install fontconfig openjdk-17-jre
        java -version
        openjdk version "17.0.8" 2023-07-18
        OpenJDK Runtime Environment (build 17.0.8+7-Debian-1deb12u1)
        OpenJDK 64-Bit Server VM (build 17.0.8+7-Debian-1deb12u1, mixed mode, sharing)

        #jenkins
        sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
        https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
        echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
        https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
        /etc/apt/sources.list.d/jenkins.list > /dev/null
        sudo apt-get update
        sudo apt-get install jenkins
        sudo systemctl start jenkins
        sudo systemctl enable jenkins 


Jenkins Pass: f1e6d5af91e64c5485451388b03d22fb

**Install Necessary Plugins in Jenkins:**

Goto Manage Jenkins → Plugins → Available Plugins →

Install below plugins

1 Eclipse Temurin Installer (Install without restart)

2 SonarQube Scanner (Install without restart)

3 NodeJs Plugin (Install Without restart)

4 Email Extension Plugin

5 Docker, Docker Commons, Docker PipelineVersion, Docker API

6 OWASP Dependency-Check

**Setup Docker Credentials in Jenkins**

![image](https://github.com/sunnyvalechha/Netflix-Clone-Project/assets/59471885/96ce39c4-2e11-4ad0-89e8-6eb592d37f05)

**Configure Java and Nodejs in Global Tool Configuration**

Goto Manage Jenkins → Tools → Install JDK(17) and NodeJs(16) → Click on Apply and Save

![image](https://github.com/sunnyvalechha/Netflix-Clone-Project/assets/59471885/7421f591-1689-4f95-98f8-066ea096b4d8)

![image](https://github.com/sunnyvalechha/Netflix-Clone-Project/assets/59471885/16884ccc-cbc0-43e9-ae9a-3a1026f00d36)

**SonarQube**

Goto SonarQube → Administration → Security → Users → Generate token named Jenkins

sonar-token: squ_d4313c119aadaa7000c531593a0260155ab590f2

![image](https://github.com/sunnyvalechha/Netflix-Clone-Project/assets/59471885/62c3ce65-df5a-4788-bffd-c9078c149a79)

Goto Jenkins Dashboard → Manage Jenkins → Credentials → Add Secret Text. It should look like this

![image](https://github.com/sunnyvalechha/Netflix-Clone-Project/assets/59471885/9fb3e506-0ff0-4299-b28c-e796a9bc621c)

**Goto Manage Jenkins → Systems → sonarqube servers → Add sonarqube**

![image](https://github.com/sunnyvalechha/Netflix-Clone-Project/assets/59471885/e06734b6-117b-40c4-a5e8-1cfd54d3874c)

Manage Jenkins → Tools → SonarQube Scanner installations

![image](https://github.com/sunnyvalechha/Netflix-Clone-Project/assets/59471885/c8abce43-b3a1-4453-a8f7-499bf09c5ef4)

Create Project in Sonar named "Netflix" → Locally → Generate 

![image](https://github.com/sunnyvalechha/Netflix-Clone-Project/assets/59471885/f66dd65b-4422-4aaa-8a69-20e4d53420c3)

![image](https://github.com/sunnyvalechha/Netflix-Clone-Project/assets/59471885/aaf103fe-9f0f-4a66-9802-96efd4596223)

![image](https://github.com/sunnyvalechha/Netflix-Clone-Project/assets/59471885/83c9e69b-5640-4a42-aae9-1af45ace69c6)

Manage Jenkins → Tools → 

Dependency-Check installations

![image](https://github.com/sunnyvalechha/Netflix-Clone-Project/assets/59471885/e3d1bac4-3874-4c9e-8bd0-e49bc0066949)

Docker

![image](https://github.com/sunnyvalechha/Netflix-Clone-Project/assets/59471885/136d2c95-e3aa-4d4b-b3ad-3bf520fab15b)

**Create Jenkins Pipeline**

New Item → Pipeline → Paste the 2nd code which automate the pipline → Change the docker account name and image name run pipeline



**Monitoring**

Launch new ec2 instance > t2.medium named Monitoring-cicd

Install Prometheus and Grafana:

First, create a dedicated Linux user for Prometheus and download Prometheus:

        sudo useradd --system --no-create-home --shell /bin/false prometheus
        wget https://github.com/prometheus/prometheus/releases/download/v2.47.1/prometheus-2.47.1.linux-amd64.tar.gz

Extract Prometheus files, move them, and create directories:

        tar -xvf prometheus-2.47.1.linux-amd64.tar.gz
        cd prometheus-2.47.1.linux-amd64/
        sudo mkdir -p /data /etc/prometheus
        sudo mv prometheus promtool /usr/local/bin/
        sudo mv consoles/ console_libraries/ /etc/prometheus/
        sudo mv prometheus.yml /etc/prometheus/prometheus.yml

        sudo service prometheus

        ls -lrth /etc/prometheus/

        sudo chown -R prometheus:prometheus /etc/prometheus/ /data/

        sudo vim /etc/systemd/system/prometheus.service

[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

StartLimitIntervalSec=500
StartLimitBurst=5

[Service]
User=prometheus
Group=prometheus
Type=simple
Restart=on-failure
RestartSec=5s
ExecStart=/usr/local/bin/prometheus \
  --config.file=/etc/prometheus/prometheus.yml \
  --storage.tsdb.path=/data \
  --web.console.templates=/etc/prometheus/consoles \
  --web.console.libraries=/etc/prometheus/console_libraries \
  --web.listen-address=0.0.0.0:9090 \
  --web.enable-lifecycle

[Install]
WantedBy=multi-user.target


sudo systemctl enable prometheus
sudo systemctl start prometheus
sudo systemctl status prometheus.service

