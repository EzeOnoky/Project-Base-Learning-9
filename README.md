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

![9_0](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/ef5cb32c-7c51-4665-b77d-872c52e7de50)


# Setup and technologies used in Project 9

- Here is how your updated architecture will look like upon competion of this project:

![PJ9_1](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/989bffbc-18a0-44f7-bb4f-e1a2a295abf1)

# INSTALL AND CONFIGURE JENKINS SERVER

## Step 1 – Install Jenkins server

### 1. - Create an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it "Jenkins"

### 2. - Install [JDK](https://en.wikipedia.org/wiki/Java_Development_Kit) (since Jenkins is a Java-based application)
Without JAVA installation, the JENKINS will not run.

```
sudo apt update
sudo apt install default-jdk-headless -y
```
I had to use Jenkins Installation Script for Ubuntu 20.04...i had to install same Ubuntu 20.04 on my EC2 Instance.

### 3. - Install Jenkins

```
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins -y
sudo systemctl status jenkins
```

I confirmed that Jenkins is up and running...

### 4. - Open TCP port 8080 on the JENKINS server
- By default Jenkins server uses TCP port 8080 – open it by creating a new Inbound Rule in your EC2 Security Group.

![PJ9_2](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/f710b73f-ba2e-4cb2-986e-09a0f6ef9644)


### 5. - Perform initial Jenkins setup.

- From my browser, i accessed **http://<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080**

http://44.201.108.157:8080  

- I was prompted to provide a default admin password
  
 ![PJ9_3](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/4e063d69-01f9-425d-8471-0194c1a6f607)
  
- I Retrieved the password from my Ubuntu server:

`sudo cat /var/lib/jenkins/secrets/initialAdminPassword`

- Then you will be asked which plugings to install – choose suggested plugins.  

![PJ9_4](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/9fcdfcc1-89f6-4b18-89cf-89d1df5a50dc)

    
- Once plugins installation is done – create an admin user and you will get your Jenkins server address. The plugin installation is helps extend the functionality of the JENKINS

![9_5](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/d3a04815-0566-45d7-b147-bff994106601)

NB - This part of creating a user can be skipped   

    
- The installation is completed!
  
  ![9_1](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/fb8137c1-6c87-4662-92b3-cad7113fd29d)


## Step 2 – Configure Jenkins to retrieve source codes from GitHub using Webhooks

- In this part, you will learn how to configure a simple Jenkins job/project (these two terms can be used interchangeably). This job will will be triggered by GitHub [webhooks](https://en.wikipedia.org/wiki/Webhook) and will execute a ‘build’ task to retrieve codes from GitHub and store it locally on Jenkins server.

### 1. - Enable webhooks in your GitHub repository settings [Webhooks]
    (https://darey.io/wp-content/uploads/2021/07/webhook_github.gif)
    
Follow the steps from  1 - 7   

![9_2](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/5f83c1ba-5063-413b-993f-07a4101ee7e8)

Below was the output diplayed on my github after i clicked on Step 7 - Add Webhook

![9_3](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/b48da83c-84a4-4325-b81a-07a116232a5a)


### 2. - Creating Job and Configuring GIT Based Push Trigger

Go to Jenkins web console, click "New Item" and create a "Freestyle project"

i had to connect to GitHub repository, and copied the URL of my Project 7 repo

![9_4](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/b79e5372-7450-4bee-ba61-b115d0299ecf)
    
![9_6](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/b6a98010-c355-479b-b035-34fe30dcb8d7)

    
In configuration of my Jenkins freestyle project, I choose Git repository, I also provided there the link to the Project 7 GitHub repository and credentials (user/password) so Jenkins could access files in the repository. `Also specify the branch containing code`....this involves changing from master to main on the branch name
    
![9_7](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/90778d41-355d-4b8f-b1ef-7204fce7abd3)


The configurations were saved, then i proceeded to run the build. For now the build can only be done manually.
Click "Build Now" button, if you have configured everything correctly, the build will be successfull and you will see it below...
  
I also proceeded to open the build and check in "Console Output" if it has run successfully. Big Congratulations! I was able to make my very first Jenkins build! Note my 1st Build failed, check Below to see the correction applied...

![9_8](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/0f59b029-858c-4e5c-a078-8a72adcd9614)


Below is the solution applied to solve the failure noticed on Build #1


![9_10](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/5103cb46-ec74-4f21-bef1-c5b0dee946a5)

   
But this build does not produce anything and it runs only when we trigger it manually. Let us fix it by configuring the bulid trigger.    

### 3. - Configuring Build Triggers
 
1 - Configure triggering the job from GitHub webhook:    
2 - Configure "Post-build Actions" to archive all the files – files resulted from a build are called "artifacts".

![9_11](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/f87e84eb-d5ca-457b-af14-6903865da3ab)

    
Now, I proceeded to make some change in any file in the GitHub repository (e.g. README.MD file) and push the changes to the master branch. I saw that a new build has been launched automatically (by webhook) and was also able to see its results – artifacts, saved on Jenkins server.  

I just added one line on my PROJECT 7 git repo, and then proceeded to commit the change
![PJ9_13](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/70426175-293a-4188-badc-f1068eb67f4f)
    
Now on checking JENKINS,  a 3rd build has been triggered by the push event, i proceeded to check more details on the 3rd build

![9_12](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/2049feb9-964f-431e-96f3-0b8aa49ecd99)

   
### SO I have now configured an automated Jenkins job that receives files from GitHub by webhook trigger
- this method is considered as ‘push’ because the changes are being ‘pushed’ and files transfer is initiated by GitHub. There are also other methods: trigger one job (downstreadm) from another (upstream), poll GitHub periodically and others.

By default, the artifacts are stored on Jenkins server locally

`ls /var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/`

NB - tooling_github is the name of the freestyle project, build number is number of the build we want to access 

So for our project 9, we will have.... So the view the Artifacts stored in JENKINS, follow below path...

`ls /var/lib/jenkins/jobs/Project_9/builds/3/archive/`


![9_13](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/65a52f03-3566-4231-be57-d8579b4cf133)

 
# CONFIGURE JENKINS TO COPY FILES TO NFS SERVER VIA SSH
    
## Step 3 – Configure Jenkins to copy files to NFS server via SSH
    
- Now we have our artifacts saved locally on Jenkins server, the next step is to copy them to our NFS server to ** /mnt/apps** directory.
    
- Jenkins is a highly extendable application and there are 1400+ plugins available. We will need a plugin that is called "[Publish Over SSH](https://plugins.jenkins.io/publish-over-ssh/)".
    
### 1. - Install "Publish Over SSH" plugin.
    
- On main dashboard select "Manage Jenkins" and choose "Manage Plugins" menu item.
    
- On "Available" tab search for "Publish Over SSH" plugin and install it.
    
![9_14](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/a47f31bb-687d-4ef1-a456-c58f25aa92c9)
    
### 2. - Configure the job/project to copy artifacts over to NFS server.
    
- On main dashboard select "Manage Jenkins" and choose "Configure System" menu item.
    
- Scroll down to Publish over SSH plugin configuration section and configure it to be able to connect to your NFS server:
    
1. - Provide a private key (content of .pem file that you use to connect to NFS server via SSH/Putty)
    
2. - Arbitrary name
    
3. - Hostname – can be **private IP address** of your NFS server
    
4. - Username – **ec2-user** (since NFS server is based on EC2 with RHEL 8)
    
5. - Remote directory – **/mnt/apps** since our Web Servers use it as a mointing point to retrieve files from the NFS server
    
- Test the configuration and make sure the connection returns **Success**. Remember, that TCP port 22 on NFS server must be open to receive SSH connections.    

![9_15](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/15721a84-4f4d-4f40-8473-8ca7754d04ff)
 
- Save the configuration, open your Jenkins job/project configuration page and add another one "Post-build Action"

- Configure it to send all files probuced by the build into our previouslys define remote directory. In our case we want to copy all files and directories – so we use **.
    
- If you want to apply some particular pattern to define which files to send – use this syntax.

![9_16](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/0bc48560-61d6-4f6a-8f0b-d8522691e39d)


![PJ9_18](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/b14f62e2-ed73-439e-b499-0c80a3f08b54)
    
- Configure it to send all files probuced by the build into our previouslys define remote directory /mnt/apps. In our case we want to copy all files and directories – so we use **.
    
- Save this configuration and go ahead, change something in **README.MD** file in your GitHub Project 7 repository.
    
- Webhook  triggered a new job and in the "Console Output" of my JENKIN job, below was seen    
    
```    
SSH: Transferred 25 file(s)
Finished: SUCCESS   
```
![9_17](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/63dba103-bc7f-4a7e-9212-e8a61b98a073)

    
- To make sure that the files in **/mnt/apps** have been udated – connect via SSH/Putty to your NFS server and check **README.MD** file

`cat /mnt/apps/README.md`

Below circled red was the extra update i made to my readme.md file of my Project 7 Github Repository

![9_18](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/7c614462-126e-4888-a50c-c55922ebbd95)


# Congratulations EZE !
You have just implemented your first Continuous Integration solution using Jenkins CI. Looking out for advanced CI configurations in upcoming projects.


## EXTRAS - Problems Face and the Solution

### 1 While updating the source Code Management on Jenkins, incase you get 403 error, use below to resolve it.

![PJ9_7](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/0749e081-85be-419a-a3c4-602809d7b502)

- If you see the changes you had previously made in your GitHub – the job works as expected.

### 2 Failed JENKIN Build Attempt - How did i fix it
    
![9_19](https://github.com/EzeOnoky/Project-Base-Learning-9/assets/122687798/b89d6e7d-fe84-4dbc-8587-4f4725954934)
