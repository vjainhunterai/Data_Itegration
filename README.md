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



[DEBUG] 08:36:18 prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql- tContextLoad_1 - Start to work.
[DEBUG] 08:36:18 prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql- tContextLoad_1 - Parameters:LOAD_NEW_VARIABLE = Warning | NOT_LOAD_OLD_VARIABLE = Warning | PRINT_OPERATIONS = false | DISABLE_ERROR = false | DISABLE_WARNINGS = true | DISABLE_INFO = true | DIEONERROR = false | 
[DEBUG] 08:36:18 prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql- tDBInput_1 - Start to work.
[DEBUG] 08:36:18 prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql- tDBInput_1 - Parameters:DB_VERSION = MYSQL_8 | USE_EXISTING_CONNECTION = false | HOST = "gpo-db.c969yoyq9cyy.us-east-1.rds.amazonaws.com" | PORT = "3306" | DBNAME = "metadata" | USER = "vineet" | PASS = enc:... | TABLE = "prod_context_L4" | QUERYSTORE = "" | QUERY = "select * from prod_context_L4" | SPECIFY_DATASOURCE_ALIAS = false | PROPERTIES = "noDatetimeStringSync=true&serverTimezone=UTC" | ENABLE_STREAM = false | TRIM_ALL_COLUMN = false | TRIM_COLUMN = [{TRIM=false, SCHEMA_COLUMN=key}, {TRIM=false, SCHEMA_COLUMN=value}] | UNIFIED_COMPONENTS = tMysqlInput | 
[DEBUG] 08:36:19 prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql- tDBInput_1 - Driver ClassName: com.mysql.cj.jdbc.Driver.
[DEBUG] 08:36:19 prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql- tDBInput_1 - Connection attempt to 'jdbc:mysql://gpo-db.c969yoyq9cyy.us-east-1.rds.amazonaws.com:3306/metadata?noDatetimeStringSync=true&serverTimezone=UTC' with the username 'vineet'.
[FATAL] 08:36:19 prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql- tDBInput_1 Access denied for user 'vineet'@'172.31.18.166' (using password: YES)
java.sql.SQLException: Access denied for user 'vineet'@'172.31.18.166' (using password: YES)
	at com.mysql.cj.jdbc.exceptions.SQLError.createSQLException(SQLError.java:129) ~[mysql-connector-java-8.0.18.jar:8.0.18]
	at com.mysql.cj.jdbc.exceptions.SQLError.createSQLException(SQLError.java:97) ~[mysql-connector-java-8.0.18.jar:8.0.18]
	at com.mysql.cj.jdbc.exceptions.SQLExceptionsMapping.translateException(SQLExceptionsMapping.java:122) ~[mysql-connector-java-8.0.18.jar:8.0.18]
	at com.mysql.cj.jdbc.ConnectionImpl.createNewIO(ConnectionImpl.java:836) ~[mysql-connector-java-8.0.18.jar:8.0.18]
	at com.mysql.cj.jdbc.ConnectionImpl.<init>(ConnectionImpl.java:456) ~[mysql-connector-java-8.0.18.jar:8.0.18]
	at com.mysql.cj.jdbc.ConnectionImpl.getInstance(ConnectionImpl.java:246) ~[mysql-connector-java-8.0.18.jar:8.0.18]
	at com.mysql.cj.jdbc.NonRegisteringDriver.connect(NonRegisteringDriver.java:199) ~[mysql-connector-java-8.0.18.jar:8.0.18]
	at java.sql.DriverManager.getConnection(Unknown Source) ~[?:1.8.0_131]
	at java.sql.DriverManager.getConnection(Unknown Source) ~[?:1.8.0_131]
	at prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql.tDBInput_1Process(Job_L4_Invoice_load_mysql.java:2240) [job_l4_invoice_load_mysql_0_1.jar:?]
	at prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql.tPrejob_1Process(Job_L4_Invoice_load_mysql.java:1755) [job_l4_invoice_load_mysql_0_1.jar:?]
	at prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql.runJobInTOS(Job_L4_Invoice_load_mysql.java:15612) [job_l4_invoice_load_mysql_0_1.jar:?]
	at prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql.main(Job_L4_Invoice_load_mysql.java:14478) [job_l4_invoice_load_mysql_0_1.jar:?]
Exception in component tDBInput_1 (Job_L4_Invoice_load_mysql)
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
	at prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql.tDBInput_1Process(Job_L4_Invoice_load_mysql.java:2240)
	at prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql.tPrejob_1Process(Job_L4_Invoice_load_mysql.java:1755)
	at prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql.runJobInTOS(Job_L4_Invoice_load_mysql.java:15612)
	at prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql.main(Job_L4_Invoice_load_mysql.java:14478)
[DEBUG] 08:36:19 prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql- tDBInput_3 - Start to work.
[FATAL] 08:36:19 prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql- tDBInput_3 Illegal key size
java.security.InvalidKeyException: Illegal key size
	at javax.crypto.Cipher.checkCryptoPerm(Cipher.java:1039) ~[?:1.8.0_121]
	at javax.crypto.Cipher.implInit(Cipher.java:805) ~[?:1.8.0_121]
	at javax.crypto.Cipher.chooseProvider(Cipher.java:864) ~[?:1.8.0_121]
	at javax.crypto.Cipher.init(Cipher.java:1396) ~[?:1.8.0_121]
	at javax.crypto.Cipher.init(Cipher.java:1327) ~[?:1.8.0_121]
	at org.talend.daikon.crypto.CipherSources$2.get(CipherSources.java:94) ~[crypto-utils-0.31.12.jar:?]
	at org.talend.daikon.crypto.CipherSources$SymmetricKeyCipherSource.encrypt(CipherSources.java:141) ~[crypto-utils-0.31.12.jar:?]
	at org.talend.daikon.crypto.Encryption.encrypt(Encryption.java:52) ~[crypto-utils-0.31.12.jar:?]
	at routines.system.PasswordEncryptUtil.encryptPassword(PasswordEncryptUtil.java:53) ~[routines.jar:?]
	at prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql$1BytesLimit65535_tDBInput_3.limitLog4jByte(Job_L4_Invoice_load_mysql.java:13989) ~[job_l4_invoice_load_mysql_0_1.jar:?]
	at prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql.tDBInput_3Process(Job_L4_Invoice_load_mysql.java:14013) [job_l4_invoice_load_mysql_0_1.jar:?]
	at prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql.tDBInput_2Process(Job_L4_Invoice_load_mysql.java:9467) [job_l4_invoice_load_mysql_0_1.jar:?]
	at prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql.runJobInTOS(Job_L4_Invoice_load_mysql.java:15627) [job_l4_invoice_load_mysql_0_1.jar:?]
	at prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql.main(Job_L4_Invoice_load_mysql.java:14478) [job_l4_invoice_load_mysql_0_1.jar:?]
Exception in component tDBInput_3 (Job_L4_Invoice_load_mysql)
java.security.InvalidKeyException: Illegal key size
	at javax.crypto.Cipher.checkCryptoPerm(Cipher.java:1039)
	at javax.crypto.Cipher.implInit(Cipher.java:805)
	at javax.crypto.Cipher.chooseProvider(Cipher.java:864)
	at javax.crypto.Cipher.init(Cipher.java:1396)
	at javax.crypto.Cipher.init(Cipher.java:1327)
	at org.talend.daikon.crypto.CipherSources$2.get(CipherSources.java:94)
	at org.talend.daikon.crypto.CipherSources$SymmetricKeyCipherSource.encrypt(CipherSources.java:141)
	at org.talend.daikon.crypto.Encryption.encrypt(Encryption.java:52)
	at routines.system.PasswordEncryptUtil.encryptPassword(PasswordEncryptUtil.java:53)
	at prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql$1BytesLimit65535_tDBInput_3.limitLog4jByte(Job_L4_Invoice_load_mysql.java:13989)
	at prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql.tDBInput_3Process(Job_L4_Invoice_load_mysql.java:14013)
	at prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql.tDBInput_2Process(Job_L4_Invoice_load_mysql.java:9467)
	at prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql.runJobInTOS(Job_L4_Invoice_load_mysql.java:15627)
	at prod_june23.job_l4_invoice_load_mysql_0_1.Job_L4_Invoice_load_mysql.main(Job_L4_Invoice_load_mysql.java:14478)



 -------------------------------------


 [Jenkins]Jenkins
Search (CTRL+K)
1
2
vineet Jain

log out
Dashboard
dockerfiletesttoecr
#44
Status
Changes
Console Output
View as plain text
Edit Build Information
Delete build ‘#44’
Git Build Data

Restart from Stage
Replay
Pipeline Steps
Workspaces
Previous Build
Console Output
Started by user vineet Jain
Obtained JenkinsFile from git https://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@github.com/GPOAdmin/Integration.git
[Pipeline] Start of Pipeline
[Pipeline] node
Running on Jenkins in /var/lib/jenkins/workspace/dockerfiletesttoecr
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Declarative: Checkout SCM)
[Pipeline] checkout
The recommended git tool is: git
using credential DockerTest
 > git rev-parse --resolve-git-dir /var/lib/jenkins/workspace/dockerfiletesttoecr/.git # timeout=10
Fetching changes from the remote Git repository
 > git config remote.origin.url https://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@github.com/GPOAdmin/Integration.git # timeout=10
Fetching upstream changes from https://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@github.com/GPOAdmin/Integration.git
 > git --version # timeout=10
 > git --version # 'git version 2.40.1'
using GIT_ASKPASS to set credentials DockerTest
 > git fetch --tags --force --progress -- https://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@github.com/GPOAdmin/Integration.git +refs/heads/*:refs/remotes/origin/* # timeout=10
 > git rev-parse refs/remotes/origin/dockertest^{commit} # timeout=10
Checking out Revision bb08d840e644356c230a81aa8fb7f8155e844ae0 (refs/remotes/origin/dockertest)
 > git config core.sparsecheckout # timeout=10
 > git checkout -f bb08d840e644356c230a81aa8fb7f8155e844ae0 # timeout=10
Commit message: "Update JenkinsFile"
 > git rev-list --no-walk 6c93340fdc29d14c25e284da2f99b30c811d30f1 # timeout=10
[Pipeline] }
[Pipeline] // stage
[Pipeline] withEnv
[Pipeline] {
[Pipeline] withEnv
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Logging into AWS ECR)
[Pipeline] script
[Pipeline] {
[Pipeline] sh
+ docker login --username AWS --password-stdin 437373745257.dkr.ecr.us-east-1.amazonaws.com
+ aws ecr get-login-password --region us-east-1
WARNING! Your password will be stored unencrypted in /var/lib/jenkins/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
[Pipeline] }
[Pipeline] // script
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Building image)
[Pipeline] script
[Pipeline] {
[Pipeline] isUnix
[Pipeline] withEnv
[Pipeline] {
[Pipeline] sh
+ docker build -t hunterai-de:L25 .
Sending build context to Docker daemon  118.1MB

Step 1/19 : FROM centos:latest
 ---> 5d0da3dc9764
Step 2/19 : ARG java_version=17
 ---> Running in 4c6bb3ff33ed
Removing intermediate container 4c6bb3ff33ed
 ---> 07e28a7d07c0
Step 3/19 : ENV JAVA_HOME /usr/lib/jvm/java-${java_version}-openjdk
 ---> Running in bdb2dbc2f587
Removing intermediate container bdb2dbc2f587
 ---> 5cd8194f9c74
Step 4/19 : ENV PATH $JAVA_HOME/bin:$PATH
 ---> Running in 6748efa2a82e
Removing intermediate container 6748efa2a82e
 ---> 1d5594a17225
Step 5/19 : RUN yum install -y java-${java_version}-openjdk-devel &&     alternatives --install /usr/bin/java java ${JAVA_HOME}/bin/java 999999 &&     alternatives --set java ${JAVA_HOME}/bin/java
 ---> Running in 41c8f07e434a
CentOS Linux 8 - AppStream                       13  B/s |  38  B     00:02    
[91mError: Failed to download metadata for repo 'appstream': Cannot prepare internal mirrorlist: No URLs in mirrorlist
[0mThe command '/bin/sh -c yum install -y java-${java_version}-openjdk-devel &&     alternatives --install /usr/bin/java java ${JAVA_HOME}/bin/java 999999 &&     alternatives --set java ${JAVA_HOME}/bin/java' returned a non-zero code: 1











