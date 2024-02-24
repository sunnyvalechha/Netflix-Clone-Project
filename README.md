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

![image](https://github.com/sunnyvalechha/Netflix-Clone-Project/assets/59471885/5d0d3289-2a2f-4272-a9d0-e97c617c5cdc)

