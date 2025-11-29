In this DevOps task, you need to build and deploy a full-stack CRUD application using the MEAN stack (MongoDB, Express, Angular 15, and Node.js). The backend will be developed with Node.js and Express to provide REST APIs, connecting to a MongoDB database. The frontend will be an Angular application utilizing HTTPClient for communication.  

The application will manage a collection of tutorials, where each tutorial includes an ID, title, description, and published status. Users will be able to create, retrieve, update, and delete tutorials. Additionally, a search box will allow users to find tutorials by title.

# step 1-  prepare the docker file ,.dockergitignore,docker-compose.yaml
.dockerinore - prevent copying unnneccesy files into images such as node_modules ,.*logs ,.idea etc do this image became small and which makes build and depoly faster
docker-compose.yaml -used to manage ,define and run multiconatiner app together . here wec define each container as service ,here we also careted custom network that is default on bridge network for conatiner communition also we attching data volumes to mangodb conatiner for dat persistence
sothat data remains in volume even after container restart ,stoped or removed,
here total four containers -ngnix-reverse-proxy/nginx stable  image in docker compose
                            frontend/dockerfile 
                            backend/dockerfile
                            mango db/stable mango image in docker-compose 
# stpe 2 push the code to github empty repo
using  - git init
        -git add .
        -git commit -m "first commit"
        -git remote add origin   https://github.com/rajeshark/discoverdollar-devops-assignment.git .
        -git branch -M main
        -git push -u origin main
        note:in company given code there a commment in tran.config.json file remove else docker build fails 
# step 3 prepare infrastructure in aws (ec2)
use AMI OF EC2 IS ubuntu
instance type -c7i-flex.large
using -degault vpc is ok now not real word
security group all ssh and http for anyware
storage is 8-gp3
![result](imges-result/infrasture-that-running-in-aws.png)

# step 4  connect to running ec2 using aws connect 
after connected to ec2 
1)update the sysem first
sudo apt update && sudo apt upgrade -y

2)install docker 
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" \
  | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io

3) Add User to Docker Group (avoid sudo)
sudo usermod -aG docker $USER
newgrp docker

4)Install Docker Compose (latest)
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" \
  -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version

5)install git for cloning
sudo apt install -y git

# step 5 after the above sofware install next github repo clone into ec2 beacse first time we do docker images and run manually then later ci/cd will deploy continously .

craete one project repo in ec2 then clone github repo to the folder using 
mkdir -p ~/deploy/dd-mean
cd ~/deploy/dd-mean
git clone https://github.com/rajeshark/discoverdollar-devops-assignment.git .

# step 6 set the .env file in dd-mean folder
using nano .env
set .env varibles 
MONGO_ROOT_USERNAME=root
MONGO_ROOT_PASSWORD=Rajesha1009
DB_NAME=dd_db
BACKEND_PORT=8080
DOCKERHUB_USERNAME=rajeshark100920

# step 7 build images loaclly during first then docker compose up
docker build -t rajesha100920/frontend ./frontend
docker build -t rajeha100920/backend ./backend
after this see the images - docker images
then using docker compose up -d  run the all containers here -d stands for detach mode
using docker compose ps see all container status 
if any problems chack logs of conatines using -docker logs container name
![result](imges-result/image-showing-ec2-docker-compose-ps-status.png)


# step 8 use public ip of ec2 access the frontend ui result in broswer

![result](imges-result/image-showing-frontend-added-tutorial.png)
![result](imges-result/image-showing-added-tututrial-of-frontend-ui.png)
![result](imges-result/image-frontend-ui-upadating-added-tutorial.png)

#step final step settiing the ci/cd pipeline
code is in .github/workflows/ci-cd.yml
set secrets for github action 

















