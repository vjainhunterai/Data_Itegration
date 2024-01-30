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


------------------------------------


java.sql.SQLException: Access denied for user 'vineet'@'172.31.18.166' (using password: YES)
	at com.mysql.cj.jdbc.exceptions.SQLError.createSQLException(SQLError.java:129)
	at com.mysql.cj.jdbc.exceptions.SQLError.createSQLException(SQLError.java:97)
	at com.mysql.cj.jdbc.exceptions.SQLExceptionsMapping.translateException(SQLExceptionsMapping.java:122)
	at com.mysql.cj.jdbc.ConnectionImpl.createNewIO(ConnectionImpl.java:836)
	at com.mysql.cj.jdbc.ConnectionImpl.<init>(ConnectionImpl.java:456)
	at com.mysql.cj.jdbc.ConnectionImpl.getInstance(ConnectionImpl.java:246)
	at com.mysql.cj.jdbc.NonRegisteringDriver.connect(NonRegisteringDriver.java:199)
	at java.sql.DriverManager.getConnection(Unknown Source)
	at java.sql.DriverManager.getConnection(Unknown Source)
	at prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql.tDBInput_1Process(Job_L4_Invoice_load_mysql.java:2135)
	at prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql.tPrejob_1Process(Job_L4_Invoice_load_mysql.java:1751)
	at prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql.runJobInTOS(Job_L4_Invoice_load_mysql.java:14255)
	at prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql.main(Job_L4_Invoice_load_mysql.java:13148)
Exception in component tDBInput_3 (Job_L4_Invoice_load_mysql)
com.mysql.cj.jdbc.exceptions.CommunicationsException: Communications link failure

The last packet sent successfully to the server was 0 milliseconds ago. The driver has not received any packets from the server.
	at com.mysql.cj.jdbc.exceptions.SQLError.createCommunicationsException(SQLError.java:174)
	at com.mysql.cj.jdbc.exceptions.SQLExceptionsMapping.translateException(SQLExceptionsMapping.java:64)
	at com.mysql.cj.jdbc.ConnectionImpl.createNewIO(ConnectionImpl.java:836)
	at com.mysql.cj.jdbc.ConnectionImpl.<init>(ConnectionImpl.java:456)
	at com.mysql.cj.jdbc.ConnectionImpl.getInstance(ConnectionImpl.java:246)
	at com.mysql.cj.jdbc.NonRegisteringDriver.connect(NonRegisteringDriver.java:199)
	at java.sql.DriverManager.getConnection(Unknown Source)
	at java.sql.DriverManager.getConnection(Unknown Source)
	at prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql.tDBInput_3Process(Job_L4_Invoice_load_mysql.java:12735)
	at prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql.tDBInput_2Process(Job_L4_Invoice_load_mysql.java:8762)
	at prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql.runJobInTOS(Job_L4_Invoice_load_mysql.java:14270)
	at prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql.main(Job_L4_Invoice_load_mysql.java:13148)
Caused by: com.mysql.cj.exceptions.CJCommunicationsException: Communications link failure

The last packet sent successfully to the server was 0 milliseconds ago. The driver has not received any packets from the server.
	at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
	at sun.reflect.NativeConstructorAccessorImpl.newInstance(Unknown Source)
	at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(Unknown Source)
	at java.lang.reflect.Constructor.newInstance(Unknown Source)
	at com.mysql.cj.exceptions.ExceptionFactory.createException(ExceptionFactory.java:61)
	at com.mysql.cj.exceptions.ExceptionFactory.createException(ExceptionFactory.java:105)
	at com.mysql.cj.exceptions.ExceptionFactory.createException(ExceptionFactory.java:151)
	at com.mysql.cj.exceptions.ExceptionFactory.createCommunicationsException(ExceptionFactory.java:167)
	at com.mysql.cj.protocol.a.NativeSocketConnection.connect(NativeSocketConnection.java:91)
	at com.mysql.cj.NativeSession.connect(NativeSession.java:144)
	at com.mysql.cj.jdbc.ConnectionImpl.connectOneTryOnly(ConnectionImpl.java:956)
	at com.mysql.cj.jdbc.ConnectionImpl.createNewIO(ConnectionImpl.java:826)
	... 9 more
Caused by: java.net.ConnectException: Connection refused (Connection refused)
	at java.net.PlainSocketImpl.socketConnect(Native Method)
	at java.net.AbstractPlainSocketImpl.doConnect(Unknown Source)
	at java.net.AbstractPlainSocketImpl.connectToAddress(Unknown Source)
	at java.net.AbstractPlainSocketImpl.connect(Unknown Source)
	at java.net.SocksSocketImpl.connect(Unknown Source)
	at java.net.Socket.connect(Unknown Source)
	at com.mysql.cj.protocol.StandardSocketFactory.connect(StandardSocketFactory.java:155)
	at com.mysql.cj.protocol.a.NativeSocketConnection.connect(NativeSocketConnection.java:65)


 -----------------


 cd /etc/yum.repos.d/
sed i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS*
sed i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS*
yum update -y
yum install -y mariadb

-----------------------


sudo rpm -Uvh https://repo.mysql.com/mysql80-community-release-el7-3.noarch.rpm


sudo sed -i 's/enabled=1/enabled=0/' /etc/yum.repos.d/mysql-community.repo


sudo yum --enablerepo=mysql80-community install mysql-community-server

service mysqld start

sudo grep "A temporary password" /var/log/mysqld.log


mysql_secure_installation 


service mysqld restart
chkconfig mysqld on

mysql -u root -p


-------------------

sudo amazon-linux-extras install epel -y 

sudo yum install https://dev.mysql.com/get/mysql80-community-release-el7-5.noarch.rpm 

sudo yum install mysql-community-server


-----------------------------------------


database url : gpo-db.c969yoyq9cyy.us-east-1.rds.amazonaws.com
port  : 3306
schema : metadata
user : vineet
Password : Gpohealth!@!

--------------------------------------------------











