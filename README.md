To install Jenkins

sudo yum install jenkins java-1.8.0-openjdk-devel -y

Start Jenkins as a service

sudo systemctl start jenkins

We also need git so install it by the following command

sudo yum install git -y

Now start docker services

sudo service docker start

Add the ec2-user to the docker group, so you can execute Docker commands without using sudo

sudo usermod -a -G docker ec2-user

After that restart Jenkins and Docker by following commands

sudo service jenkins restart

sudo service docker restart


---------------


pipeline {
 agent any
 environment {
 AWS_ACCOUNT_ID="YOUR_ACCOUNT_ID_HERE"
 AWS_DEFAULT_REGION="CREATED_AWS_ECR_CONTAINER_REPO_REGION" 
 IMAGE_REPO_NAME="ECR_REPO_NAME"
 IMAGE_TAG="latest"
 REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
 }
 
 stages {
 
 stage('Logging into AWS ECR') {
 steps {
 script {
 sh "aws ecr get-login-password - region ${AWS_DEFAULT_REGION} | docker login - username AWS - password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"
 }
 
 }
 }
 
 stage('Cloning Git') {
 steps {
 checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/sd031/aws_codebuild_codedeploy_nodeJs_demo.git']]]) 
 }
 }
 
 // Building Docker images
 stage('Building image') {
 steps{
 script {
 dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
 }
 }
 }
 
 // Uploading Docker images into AWS ECR
 stage('Pushing to ECR') {
 steps{ 
 script {
 sh "docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"
 sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"
 }
 }
 }
 }
}



-----------------------

437373745257.dkr.ecr.us-east-1.amazonaws.com/hunterai-de:latest


--------------------------


[ec2-user@ip-172-31-18-166 ~]$ mysql -h gpo-db.c969yoyq9cyy.us-east-1.rds.amazonaws.com -u rdsdevadmin -p
Enter password:
ERROR 2059 (HY000): Authentication plugin 'caching_sha2_password' cannot be loaded: /usr/lib64/mysql/plugin/caching_sha2_password.so: cannot open shared object file: No such file or directory






