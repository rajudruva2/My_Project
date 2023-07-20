# My_Project

CICD Document 

servers' information
eq-demo-lm1: Kubernetes worker node-1 
eq-demo-lm2: wso2 and Jfrog artifactory 
eq-demo-lm3: Kubernetes master node
eq-demo-lm4: Kubernetes worker node-2
eq-demo-lm5: Jenkins's server

1) Gitlab repository (docker file, Jenkins file, deployment yaml file and service yaml file) 
URL: https://gitlab.com/rajudruva2/maven-project2
        Maven-project2

2) Jenkins (VM name: eq-demo-lm5)
 (Git installation, Maven installation, Docker installation, Java installation,)
URL: http://20.204.131.181:8080/
Jenkins Installation:
-$   Sudo yum update –y
-$   cd /opt
-$   sudo wget -O /etc/yum.repos.d/jenkins.repo \    https://pkg.jenkins.io/redhat-stable/jenkins.repo
-$   sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
-$   sudo yum upgrade

Java installation :
-$   sudo amazon-linux-extras install java-openjdk11orSudo yum install java –y
-$   yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
-$   sudo yum install epel-release java-11-openjdk-devel
-$   sudo yum install jenkins –y
-$   systemctl start jenkins
-$   systemctl status jenkins
-$   systemctl enable jenkins
-$   http://<PublicIP>:8080
-$   cat /var/lib/jenkins/secrets/initialAdminPassword

 

Git Installation:
-$        sudo yum install git –y
-$   git version

Maven installation:
-$   cd /opt
-$   sudo wget https://downloads.apache.org/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz
-$   sudo tar -xzvf /opt/apache-maven-3.6.3-bin.tar.gz
-$   sudo ln -s /opt/apache-maven-3.6.3 /opt/maven
-$   mvn –version

Docker installation:
-$   yum install docker –y
-$      sudo groupadd docker
-$   sudo groupadd docker eqadmin
-$   sudo groupadd jenkins
-$   sudo usermod -aG docker jenkins
-$   docker version
-$   sudo systemctl start docker
-$   sudo systemctl enable docker
-$   sudo systemctl status docker

Path Setup
#Open the .bash_profile 
-$   sudo vi .bash_profile
#Paste the following data :
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-11.0.13.0.8-1.0.1.el7_9.x86_64
export MAVEN_HOME=/opt/maven
export PATH=${MAVEN_HOME}/bin:${PATH}:${JAVA_HOME}/bin:${JFROG_HOME}/app/bin:/opt/kube 

Jfrog Installation VM name: eq-demo-lm2
-$   cd /opt
-$   sudo wget https://releases.jfrog.io/artifactory/bintray-artifactory/org/artifactory/jcr/jfrog-artifactory-jcr/[RELEASE]/jfrog-artifactory-jcr-[RELEASE]-linux.tar.gz
-$   sudo tar -xvzf jfrog-artifactory-jcr-[RELEASE]-linux.tar.gz
-$   sudo mv artifactory-jcr-7.27.10/ jfrog
-$   sudo ./jfrog/app/bin/installService.sh
-$   sudo systemctl start artifactory.service
-$   sudo systemctl status artifactory.service
 #access the server--
http://IP:8081(set password and https://jfrog-custom.com as base url)
#Fallow the below document to create docker repositories,
https://www.jfrog.com/confluence/display/JFROG/Docker+Registry
•	Getting Started with Artifactory Pro On-Prem: -
 Using Without a reverse proxy:
1)Configuring Artifactory:
 #To configure Artifactory to use the Repository Path method, carry out the following steps:
 #Make sure Artifactory is up and running and is activated with a valid license.
 #Create your virtual Docker repository (as well as a local and remote Docker repository that it should aggregate). In our #example below we will use a repository named docker-virtual.
 Make sure bellow,
 Go to the HTTP Settings page from the Administration module under Artifactory | General | HTTP Settings.
 In the Docker Settings panel, select Repository Path as the Docker Access Method.
 #You must use Embedded Tomcat
 #You can only use Artifactory as a Docker registry without a reverse proxy by using the internal embedded Tomcat.
            2) Configuring Your Docker Client: {Jenkins Server}
            #Using the Repository Path method, you can work with Artifactory as a Docker registry without a reverse proxy on an #insecure connection (i.e. only HTTP is supported, not HTTPS). You need to configure the Docker client to work with an #insecure registry as described in the Docker documentation.

•	#Restart your Docker daemon/engine to apply the insecure registry flag (if self-signed certificate is imported, you do not #need to restart the Docker daemon/engine). Running $docker info will list the Insecure registries that have been applied #under the Insecure Registries entry.
Docker documentation:
Test an insecure registry,
-$   sudo vi /etc/docker/daemon.json
copy bellow
{
"insecure-registries" : ["<ARTIFACTORY_IP>:<PORT>"]
}
#Restart Docker for the changes to take effect:
-$  sudo systemctl daemon-reload
-$  sudo systemctl restart docker
-$  docker info
              3)Artifactory connection check:
#Should verify that your Docker client can access Artifactory by run the following command. Making sure that the return #code is 200
-$  curl -I -k -v -u <USER_NAME> -p <PASSWORD> http://:/artifactory/api/system/ping

docker commands:
-$   docker login -u <USER_NAME> -p <PASSWORD> http:/<ARTIFACTORY_IP>:<PORT>
-$   docker tag <IAMGE_NAME>:<TAG_NAME> <{ARTIFACTORY_IP>:<ARTIFACTORY_PORT>/<ARTIFACTORY_KEY>/<IAMGE_NAME>:<TAG_NAME>
-$   docker push <ARTIFACTORY_IP>:<ARTIFACTORY_PORT>/<ARTIFACTORY_KEY>/<IAMGE_NAME>:<TAG_NAME>
-$   docker pull <ARTIFACTORY_IP>:<PORT>/<ARTIFACTORY_KEY>/<IMAGE_NAME>:<TAG>

Kubernetes Cluster Setup 
Note: There is separate document on Kubernetes setup so not mentioned here.
 

CICD Process:
1) Create a Pipeline Job (Docker)
2) Go to Docker job
3) Configure Job

 

4) Go to pipeline
5) Select definition as pipeline script as SCM
 
6) Give the Git Repository URL
https://gitlab.com/rajudruva2/maven-project2.git
7) Give the Git Credentials
8) Select the Branch as master
9) Script path as Jenkins file

 
10) Go to the Gitlab (maven –project2 repository) and write the jenkins file as below :
 
 
 
YAML:
Maven-project2 deployment yaml
 
Maven-project2 service yaml
 
Docker File :
 

11) Add Parameters in Jenkins Job
 
 
 
 
 
15) Required Plugin to Run Jenkins Job 
A) Artifactory Plugin 
B) Docker Plugin 
C) Git Plugin
D) Kubernetes Plugin
E) Maven Plugin
F) SSH pipeline Steps
16)Triggered the Jenkins pipeline job which has done the following things 
a.	checked out code from GitLab.
b.	built the java app.
c.	created the docker image with java app.
d.	pushed the image to Jfrog Artifactory.
e.	deployed the image to Kubernetes cluster.
17) Access the application from outside with 
      http://<nodeip>:serviceport
