<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Deploy a Web App with CodeDeploy

**Project Link:** [View Project](http://learn.nextwork.org/projects/aws-devops-codedeploy-updated)

**Author:** davidniiamui@gmail.com  
**Email:** niiobdavid@gmail.com

---

![Image](http://learn.nextwork.org/genuine_navy_mysterious_monkey/uploads/aws-devops-codedeploy-updated_val-27)

---

## Introducing Today's Project!

In this project, I will demonstrate how to use Coddeply to deploy a web app
I'm doing this project to learn how deployment works, and how it can be automated using a combination of CodeDeploy and deployment scripts.

### Key tools and concepts

Services we used were CodeDeploy, CodeBuild, CodeArtifact, IAM, CloudFormation, EC2, S3, CodeConnection, VS Code and GitHub. 
Key concepts we learnt include deployment, deployment groups, deployment scripts, appspec.yml, and the importance of rebuilding your project before you deploy!

### Project reflection

This project took me approximately 3 hours, including
troubleshooting and documentation time. 
The most challenging part was running into a 'QUEUED error' with the build that I wasn't able to figure out. However, once I restarted the process, it worked the second time.
It was most rewarding to see the deployed web app.

This project is part five of a series of DevOps projects where I'm building a CI/CD pipeline! I'll be working on the next project tonight!😗🍵👨🏽‍💻

---

## Deployment Environment

To set up for CodeDeploy, I launched an EC2 instance and VPC because they will become/make up the production environment.
I need to separate the development environment (where the code is being written) from the production environment (where the web app is being deployed) so that the code being written by developers doesn't get shown to users until it's ready to be pushed to production.

Instead of launching these resources manually, I used CloudFormation.
When I need to delete these resources created by the CloudFormation template, I can simply delete the CloudFormation stack.
This will automatically delete all the resources that are inside the stack.

Other resources created in this template include networking resources like VPCs, internet gateways, route tables, and subnets.
They're also in the template because a production environment has specific requirements around traffic that it should enable and block out
Setting up all of the networking resources that will help the deployment work is common practice.

![Image](http://learn.nextwork.org/genuine_navy_mysterious_monkey/uploads/aws-devops-codedeploy-updated_val-5)

---

## Deployment Scripts

Scripts are mini programs that help us automate the running of commands.
To set up CodeDeploy, I also wrote scripts to automate deployment commands.
These are commands that my deployment EC2 instance needs to run in order to host the web app.

'install_dependencies.sh' will help my EC2 instance install all the dependencies it needs to host a web app, like Tomcat (a web server)

'start_server.sh' is another script that's all about starting up the 2 servers that I'm using in the EC2  instance.
1. It starts up Tomcat (responsible for running my Java web app)
2. It starts Apache (the web server responsible for handling web traffic and passing requests to Tomcat).

'stop_server.sh' is a script that stops the Apache and Tomcat servers when they are no longer needed to serve the web app to the user.

---

## appspec.yml

Then, I wrote an appspec.yml file to give  CodeDeploy the instructions for deploying the web app.
The key sections in appspec.yml are:
- BeforeInstall (use the 'install_dependencies.sh' script to install dependencies like Tomcat)
- ApplicationStart (use the 'start_server.sh') and
- ApplicationStop (use the 'stop_server.sh' script).

I also updated buildspec.yml to tell CodeBuild that it should also package up the new appspec.yml file and set up scripts that I created inside the build artifact (the compressed war file)

![Image](http://learn.nextwork.org/genuine_navy_mysterious_monkey/uploads/aws-devops-codedeploy-updated_val-12)

---

## Setting Up CodeDeploy

A deployment group is a group of EC2 instances that you can deploy to, and also a collection of settings that determine how you want to deploy your web app.
A CodeDeploy application is a folder that holds together all the deployment groups for the same app.

To set up a deployment group, you also need to create an IAM role to give CodeDeploy the permission to access the EC2 instances that it needs to coordinate. Otherwise, CodeDeploy doesn't have access to EC2 = not being able to give your deployment instances the instructions for deploying the web app.

Tags are helpful for identifying the instances that will be deploying the web app.
I used the tag 'role:webserver' to automatically match the EC2 instance I deployed with my CloudFormation template.
Now in future, when I have other EC2 instances I want to also be in this deployment group, I can simply tag that new instance with the same 'role:webserver' tag.
They'll automatically get added to the same deployment group (a good timesaver). 

![Image](http://learn.nextwork.org/genuine_navy_mysterious_monkey/uploads/aws-devops-codedeploy-updated_val-18)

---

## Deployment configurations

Another key settings is the deployment configuration, which affects how quickly and risky the web app is deployed.
I used CodeDeployDefault.AllAtOnce, so any changes that CodeDeploy deploys, affects ALL the instances in the deployment group at once.
This is the fastest way to deploy a change/code update.

In order to connect the deployment instance with CodeDeploy, a CodeDeploy Agent is also set up to receive instructions from CodeDeploy, and make sure that the commands in appspec.yml are run.

![Image](http://learn.nextwork.org/genuine_navy_mysterious_monkey/uploads/aws-devops-codedeploy-updated_val-20)

---

## Success!

A CodeDeploy deployment is a specific update to the application that will be deployed to its users.
The difference between a deployment group and a deployment is that the group is like a settings file and the deployment itself is like a specific update rolled out using the settings file.

I had to configure a revision location, which means where the web app's WAR file (the compressed file ready to be deployed) lives.
My revision location is the S3 bucket that I created, linked to the CodeBuild (build artifacts automatically go into that bucket).

To check that the deployment was successful, I visited the IPv4 DNS address of my deployment EC2 instance.
I saw a live web app that's working and serving the web app's code to end users

![Image](http://learn.nextwork.org/genuine_navy_mysterious_monkey/uploads/aws-devops-codedeploy-updated_val-27)

---

## Disaster Recovery

---

---
