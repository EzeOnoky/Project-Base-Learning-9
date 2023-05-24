# Project-Base-Learning-9
DEPLOYING JENKINS_CONTINOUS INTEGRATION PIPELINE FOR TOOLING WEBSITE


## PROJECT TASK
Enhance the architecture prepared in Project 8 by adding a Jenkins server, configure a job to automatically deploy source codes changes from Git to NFS server.


## BACKGROUND KNOWLEDGE : INTRODUCTION TO JENKINS
Jenkins is one of the tools DevOps Engineers use for continuous integration, constantly releasing softwares.

- In previous Project 8 we introduced **horizontal scalability** concept, which allow us to add new Web Servers to our Tooling Website and you have successfully deployed a set up with 2 Web Servers and also a Load Balancer to distribute traffic between them. If it is just two or three servers – it is not a big deal to configure them manually. Imagine that you would need to repeat the same task over and over again adding dozens or even hundreds of servers.

- DevOps is about Agility, and speedy release of software and web solutions. One of the ways to guarantee fast and repeatable deployments is **Automation** of routine tasks.


- In this project we are going to start automating part of our routine tasks with a free and open source automation server – [Jenkins](https://en.wikipedia.org/wiki/Jenkins_(software)). It is one of the mostl popular [CI/CD](https://en.wikipedia.org/wiki/CI/CD) tools, it was created by a former Sun Microsystems developer Kohsuke Kawaguchi and the project originally had a named "Hudson".

- Acording to Circle CI, **Continuous integration (CI)** is a software development strategy that increases the speed of development while ensuring the quality of the code that teams deploy. Developers continually commit code in small increments (at least daily, or even several times a day), which is then automatically built and tested before it is merged with the shared repository.

- In our project we are going to utilize Jenkins CI capabilities to make sure that every change made to the source code in GitHub **https://github.com/yourname/tooling**  **[https://github.com/EzeOnoky/Project-Base-Learning-7]** will be automatically be updated to the Tooling Website.

- ***Side Self Study***

- Read about [Continuous Integration](https://circleci.com/continuous-integration/), [Continuous Delivery](https://circleci.com/continuous-integration/) and [Continuous Deployment](https://circleci.com/continuous-integration/).

## Setup and technologies used in Project 9

- Here is how your updated architecture will look like upon competion of this project:

![PJ9_1](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/989bffbc-18a0-44f7-bb4f-e1a2a295abf1)

## INSTALL AND CONFIGURE JENKINS SERVER

### Step 1** – Install Jenkins server

#### 1. - Create an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it "Jenkins"

#### 2. - Install [JDK](https://en.wikipedia.org/wiki/Java_Development_Kit) (since Jenkins is a Java-based application)

```
sudo apt update
sudo apt install default-jdk-headless
```

#### 3. - Install Jenkins

```
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt-get install jenkins
```

- Make sure Jenkins is up and running   `sudo systemctl status jenkins`

#### 4. - Open TCP port 8080 on the JENKINS server
- By default Jenkins server uses TCP port 8080 – open it by creating a new Inbound Rule in your EC2 Security Group.

![PJ9_2](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/f710b73f-ba2e-4cb2-986e-09a0f6ef9644)


#### 5. - Perform initial Jenkins setup.

- From your browser access **http://<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080**

- You will be prompted to provide a default admin password
  
 ![PJ9_3](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/4e063d69-01f9-425d-8471-0194c1a6f607)
  
- Retrieve it from your server:

`sudo cat /var/lib/jenkins/secrets/initialAdminPassword`

- Then you will be asked which plugings to install – choose suggested plugins.  

![PJ9_4](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/9fcdfcc1-89f6-4b18-89cf-89d1df5a50dc)

- Once plugins installation is done – create an admin user and you will get your Jenkins server address.

- The installation is completed!

  ![PJ9_5](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/c7d2881f-0955-492e-bb11-624060e51610)
  

###   
