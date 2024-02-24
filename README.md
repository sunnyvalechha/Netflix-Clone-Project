# Netflix-Clone-Project
DevSecOps Project - Netflix Clone by Cloud Champ

Github URL: https://github.com/N4si/DevSecOps-Project

    sudo apt-get update

    git clone https://github.com/N4si/DevSecOps-Project.git

Install Docker

    sudo apt-get update
    sudo apt-get install docker.io -y
    sudo usermod -aG docker $USER  # Replace with your system's username, e.g., 'ubuntu'
    newgrp docker
    sudo chmod 777 /var/run/docker.sock

Go to The movie database (TMDB)

    https://www.themoviedb.org/

![image](https://github.com/sunnyvalechha/Netflix-Clone-Project/assets/59471885/791326d6-777e-4530-9efa-7004c60e8732)

    docker build --build-arg TMDB_V3_API_KEY=55e71f920f05dd2ed29334de060ad77d -t netflix .

    docker run -d -p 8081:80 a67754fda871

![image](https://github.com/sunnyvalechha/Netflix-Clone-Project/assets/59471885/ff1ffd7e-9ac0-401f-9c89-e2db967f6e47)

Aws Public IP with port 8081

![image](https://github.com/sunnyvalechha/Netflix-Clone-Project/assets/59471885/5d0d3289-2a2f-4272-a9d0-e97c617c5cdc)

Run Sonarqube for Security Part

        docker run -d --name sonar -p 9000:9000 sonarqube:lts-community

        http://52.91.124.230:9000/

        Username / Password: admin
        New Password: pass

Install Trivy

        sudo apt-get install wget apt-transport-https gnupg lsb-release
        wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
        echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list
        sudo apt-get update
        sudo apt-get install trivy

To scan the file system

        trivy fs .

To scan the image using trivy run below command, Image is netflix image, If found any High Vulnerability so the Developer should make the changes in image

        trivy image a67754fda871

Install Jenkins on the EC2 instance to automate deployment: Install Java

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

        Jenkins Pass: 318b9e0132f24ebe8b217e37ab68eddd

Install Necessary Plugins in Jenkins:

Goto Manage Jenkins →Plugins → Available Plugins →

Install below plugins

1 Eclipse Temurin Installer (Install without restart)

2 SonarQube Scanner (Install without restart)

3 NodeJs Plugin (Install Without restart)

4 Email Extension Plugin

![image](https://github.com/sunnyvalechha/Netflix-Clone-Project/assets/59471885/a8a932ac-e846-4f61-a710-7d60366129ec)

Configure Java and Nodejs in Global Tool Configuration

Goto Manage Jenkins → Tools → Install JDK(17) and NodeJs(16)→ Click on Apply and Save

![image](https://github.com/sunnyvalechha/Netflix-Clone-Project/assets/59471885/35ed9449-8f53-4097-bcba-ead5763bb956)

![image](https://github.com/sunnyvalechha/Netflix-Clone-Project/assets/59471885/f6dc7498-f8d4-4538-843c-2523ae99cd36)

SonarQube
Create the token

Goto SonarQube > Administration > Security > Users > Generate token named Jenkins

squ_554da5bf814059759bddc7bfd645e550aa2224ac

![image](https://github.com/sunnyvalechha/Netflix-Clone-Project/assets/59471885/6edd2740-b0a4-4761-af83-cdc8367fca11)


Goto Jenkins Dashboard → Manage Jenkins → Credentials → Add Secret Text. It should look like this

![image](https://github.com/sunnyvalechha/Netflix-Clone-Project/assets/59471885/161a566a-3677-4802-ab7e-9251485c9240)

After adding sonar token

Click on Apply and Save

![image](https://github.com/sunnyvalechha/Netflix-Clone-Project/assets/59471885/0d360347-6e0a-4a11-b890-934d155fc2f9)

The Configure System option is used in Jenkins to configure different server

Global Tool Configuration is used to configure different tools that we install using Plugins

We will install a sonar scanner in the tools.

Manage Jenkins > Tools > SonarQube Scanner installations

![image](https://github.com/sunnyvalechha/Netflix-Clone-Project/assets/59471885/fae5d998-f2a6-4c3b-b66d-495b85674114)


So the pipeline is ready to deploy the application 

Configure CI/CD Pipeline in Jenkins:

Create a CI/CD pipeline in Jenkins to automate your application deployment.

Dashboard > NewItem > paste the code > apply & save > run the pipeline (build now)

        pipeline {
    agent any
    tools {
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }
    stages {
        stage('clean workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout from Git') {
            steps {
                git branch: 'main', url: 'https://github.com/N4si/DevSecOps-Project.git'
            }
        }
        stage("Sonarqube Analysis") {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Netflix \
                    -Dsonar.projectKey=Netflix'''
                }
            }
        }
        stage("quality gate") {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-token'
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
    }
}



Create a Jenkins webhook

