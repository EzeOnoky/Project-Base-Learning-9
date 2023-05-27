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

# INSTALL AND CONFIGURE JENKINS SERVER

## Step 1 – Install Jenkins server

#### 1. - Create an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it "Jenkins"

#### 2. - Install [JDK](https://en.wikipedia.org/wiki/Java_Development_Kit) (since Jenkins is a Java-based application)
Without JAVA installation, the JENKINS will not run.

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

- From my browser, i accessed **http://<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080**

- I was prompted to provide a default admin password
  
 ![PJ9_3](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/4e063d69-01f9-425d-8471-0194c1a6f607)
  
- I Retrieved the password from my Ubuntu server:

`sudo cat /var/lib/jenkins/secrets/initialAdminPassword`

- Then you will be asked which plugings to install – choose suggested plugins.  

![PJ9_4](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/9fcdfcc1-89f6-4b18-89cf-89d1df5a50dc)

    
- Once plugins installation is done – create an admin user and you will get your Jenkins server address. The plugin installation is helps extend the functionality of the JENKINS

![PJ9_4A](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/af26eabc-6512-49e8-89c6-9fba1705e2ef)

NB - This part of creating a user can be skipped   

    
- The installation is completed!

  ![PJ9_5](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/c7d2881f-0955-492e-bb11-624060e51610)
  

## Step 2 – Configure Jenkins to retrieve source codes from GitHub using Webhooks

- In this part, you will learn how to configure a simple Jenkins job/project (these two terms can be used interchangeably). This job will will be triggered by GitHub [webhooks](https://en.wikipedia.org/wiki/Webhook) and will execute a ‘build’ task to retrieve codes from GitHub and store it locally on Jenkins server.

#### 1. - Enable webhooks in your GitHub repository settings [Webhooks]
    (https://darey.io/wp-content/uploads/2021/07/webhook_github.gif)
    
Follow the steps from  1 - 7   

![PJ9_6](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/e6d86242-11c7-4b2d-a2a7-044e79ad0662)
    
#### 2. - Go to Jenkins web console, click "New Item" and create a "Freestyle project"     

![PJ9_8](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/aee48333-21da-4710-b7c3-eeaf22c1ecbf)
    
i had to connect to GitHub repository, and copied the URL of the Tooling repo

![PJ9_7](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/0749e081-85be-419a-a3c4-602809d7b502)
    
In configuration of my Jenkins freestyle project, I choose Git repository, I also provided there the link to the Tooling GitHub repository and credentials (user/password) so Jenkins could access files in the repository.
    
![PJ9_9](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/89449f1b-2a85-4362-992b-1b8e29b7c814)
    
The configurations were saved, then i proceeded to run the build. For now the build can only be done manually.
Click "Build Now" button, if you have configured everything correctly, the build will be successfull and you will see it under #1 below...

![PJ9_10](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/0224b2d4-db09-4e06-a44c-e2efbce50f4b)
    
So i proceeded to open the build and check in "Console Output" if it has run successfully. Congratulations! I was able to make my very first Jenkins build!

![PJ9_11](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/5eb9273d-a3ba-4682-9bcf-9776d8c6a03e)
   
But this build does not produce anything and it runs only when we trigger it manually. Let us fix it.    

#### 3. - I Clicked "Configure" your job/project and proceeded to add these two configurations below
 
1 - Configure triggering the job from GitHub webhook:    
2 - Configure "Post-build Actions" to archive all the files – files resulted from a build are called "artifacts".

    
![PJ9_12](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/ec0934f9-63eb-4191-80d9-51e80d69dfea)
    
Now, I proceeded to make some change in any file in the GitHub repository (e.g. README.MD file) and push the changes to the master branch. I saw that a new build has been launched automatically (by webhook) and was also able to see its results – artifacts, saved on Jenkins server.  

I just added one line on my git repo, and then proceeded to commit the change
![PJ9_13](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/70426175-293a-4188-badc-f1068eb67f4f)
    
Now on checking JENKINS,  a 3rd build has been triggered by the push event, i proceeded to check more details on the 3rd build

![PJ9_14](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/39b071bc-b582-4b5b-bddf-8abd55c75394)
    
You have now configured an automated Jenkins job that receives files from GitHub by webhook trigger (this method is considered as ‘push’ because the changes are being ‘pushed’ and files transfer is initiated by GitHub). There are also other methods: trigger one job (downstreadm) from another (upstream), poll GitHub periodically and others.

By default, the artifacts are stored on Jenkins server locally

`ls /var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/`
    
# CONFIGURE JENKINS TO COPY FILES TO NFS SERVER VIA SSH
    
## Step 3 – Configure Jenkins to copy files to NFS server via SSH
    
    

    
    
