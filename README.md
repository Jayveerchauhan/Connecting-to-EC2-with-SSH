![docker](https://github.com/user-attachments/assets/6c7c22f8-96c3-4340-a196-fd9b02faf582)# How to launch and work with EC2-AWS instances

## Introduction

* Training and fine-tuning Convolutional Neural Networks (CNNs) requires high computational power. 
    
* Machines with Graphics Processing Units (GPUs) are well suited for these type of tasks but laptops and desktop computers are rarely equipped with GPUs.
    
* The most common solution is to use cloud computing services such as Amazon Web Services (AWS) and to perform complex computation tasks in the cloud. Many other cloud computer services can be used for this purpose, and you may have access to a high performing computer cluster at your home institution. In these notes we'll focus on using AWS because that's what we use and know better. 

Steps:
1. [Create an AWS account](#1-create-an-aws-account).

2. [Create an EC2 instance](#2-create-an-ec2-instance).

3. [Access your EC2 instance](#3-access-your-ec2-instance).

4. [Stopping the EC2 instance](#6-important-stopping-your-ec2-instance).

## 1. Create an AWS account
* Go to [https://aws.amazon.com/](https://aws.amazon.com/) and click on `Create a new AWS account` (upper right corner).

* Fill out the Sign Up form. 

* You will be asked to provide a credit card. New accounts can use some types of EC2 instances and other services for free during the first year. You can learn more about it in [here](https://aws.amazon.com/free/). However, EC2 instances with GPUs are not included in this free tier. A `g2.2xlarge` instance for example costs about $.65/hour (and a `p2.xlarge` instance costs about $.90/h; they both have 1 GPU), but you will be charged only for the time you use it (as long as you remember to stop it when you are done! We'll get to this later, or jump ahead to the last step to learn more).

* For security purposes, AWS highly recommends that you do not log in with as the "root" user (the user you just created, linked to your credit card). AWS suggests that you [create a new user for yourself using their IAM famework](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html). You can give that new user full administrator priviledges. The only AWS element that an IAM administrative user will not be able to see is billing information -- for that you will have to log in as the root user. We won't go into the IAM user step in detail here, as you can do the rest of this module as the root user. However, we highly recommend creating an IAM user by [following the steps in this tutorial](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html), then signing into AWS as that IAM user going forward. 

* Finally, folks affiliated with a university should consider signing up for [AWS Educate](https://aws.amazon.com/education/awseducate/). Once you apply and are approved, you'll receive free AWS credits (from $30-200 depending on your status). Those credits will be more than enough to cover the AWS services you'll use during this workshop.
## 2. Create an EC2 instance

[What are EC2 instances?](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html) These are servers mantained by AWS that you can use remotely. It's like renting a computer from Amazon for as long as you need it. These computers, however, do not have a keyboard or a mouse, so you'll have to interact with them remotely and programatically from your local machine. We'll give you some hints on how to do this. But first, let's go through how to create an instance with everything already set up to implement state-of-the-art computer vision techniques.

* Sign into your AWS account. Click on `Services` and then on `EC2`. This will bring you to your `EC2 Dashboard`. Now click on the `Instances` option under the Instances menu (left side of the screen). This will bring up your instances board.

* To create a new instance, click on the blue `Launch Instance` button. You'll then have to go through different steps to specify the type of server you want to create:

    - **Step 1: Choose and Amazon Machine Image (AMI).** In here you need to specify the type of operation system (OS) and pre-settings you want installed in your machine. Installing all the needed dependencies for deep learning and computer vision is not an easy task, as several software and hardware requirements need to be met. Lucky for us, other people have already gone through the hassle and have then openly shared their pre-settings! To access these, select the `Community AMIs` from the menu on the left. You'll then see an almost infinite list of AMIs to select from. One that we have already tested and know works well to implement and fine-tune computer vision models in python (e.g. using `PyTorch`) is the `Deep Learning AMI (Ubuntu) Version 3.0 - ami-0a9fac70`. Simply type **ami-0a9fac70** in the search bar and select the only result that will pop up. 
    
    - If you search for **ami-0a9fac70** and nothing comes up, your AWS account is probably looking in the wrong region. This AMI is only available in the `US East (N. Virginia)` region. In the upper right portion of the AWS dashboard, just to the left of the word `Support`, you'll see a dropdown option. If you click on it, you will see a list of all the AWS regions. Make sure that `US East (N. Virginia)` is selected, then search again for **ami-0a9fac70**.
    
    - **Step 2: Choose an Instance Type.** Here you need to specify the type of machine/computer you want. You'll see a lot of types, which vary on several dimensions, such as the memory size and the number of computing nodes (CPUs, GPUs). The `g` and `p` instance types have GPU graphics on them. An affordable option ($.65/h) is to use the `g2.2xlarge` type: it comes with 1 GPU and a decent amount of memory (15Gb). The next affordable option ($.90/h) is the `p2.xlarge`, which also comes with 1 GPU (although more efficent/fast, see this [comparison](https://bitfusion.io/2016/11/03/quick-comparison-of-tensorflow-gpu-performance-on-aws-p2-and-g2-instances/)) and much more memory (61Gb). Once you have selected an instance type, click on `Review and Launch`
    
    - **Step 3,4,5,6.** You can skip these steps for now. If asked, simply leave the default options selected.
    
    - **Step 7: Review.** Click on `Launch`. You'll be asked to "Create a new key pair" (or to "Choose an existing key pair" if you have already launched a EC2 instance using this account). Choose a name for the key pair and click on `Download Key Pair`. This will download a `.pem` file, **save it in a safe place and NEVER lose it**. This file is used to access the instance and if you lose it, you won't be able to ever access it again! For now, you can save the file to your Desktop.
    
    - Once you have downloaded the key and clicked on `Launch Instances`, the instance will be created and started. You can see it active if you go back to the Instances board. It may take a few minutes to finish initializing.
    
    - If you get an error when you try to launch the instance, something like "Launch Failed -- You have requested more instances (1) than your current instance limit of 0 allows for the specified instance type. Please visit http://aws.amazon.com/contact-us/ec2-request to request an adjustment to this limit", you will need to follow the link and request access to these types of instances from AWS support. **It may take a few days to get this permission!!!**. You'll have the option to request some number of this instance type (either `g2.2xlarge` or `p2.xlarge`). We suggest that you request only one instance of the type you've chosen, since that may make the approval process go more quickly. Once you receive approval, go through these steps again to launch your instance.
    
* NOTE: If you get this far in the module and have to stop, **make sure you STOP your EC2 instance** to avoid being charged continuously. See the last step of this guide. 
## 3. Access your EC2 instance.

* Once your EC2 instance has been launched, you can access your instance programmatically. Make sure you are logged in to your AWS account, preferrably as your IAM user, and `start` your EC2 instance if it is `stopped`. You can check whether or not the instance is on by looking at the EC2 Instances board. If the Instance State is `stopped`, right click on the word `stopped`, then hover over the Intstance State option and select `start`. It will ask if you are sure you want to start the instance. 

* Open a bash terminal on your machine. Mac computers already [come with bash terminal](https://macpaw.com/how-to/use-terminal-on-mac). Windows computers often don't, but a you can get one by downloading [Git for Windows](https://gitforwindows.org/): once installed, simply a) click on the Windows or Start icon, b) in the Programs list, open the Git folder, and c) click the option for Git Bash.

* Here is the command you need to type in the bash shell in order to access the EC2 instance: `ssh -i xxxx.pem ubuntu@xx.xxx.xxx.xxx`, where:
    
    - `xxxx.pem` is the path to your key. This could be something like: `/Users/johnsmith/my_key.pem`. If it's the first time you use this key, you'll need to set up the right permission by typing in a bash terminal: `sudo chmod 600 /path/to/my/key.pem`. Otherwise, you'll get the following warning: `WARNING: UNPROTECTED PRIVATE KEY FILE!`.
    - `xx.xxx.xxx.xxx` is the IP of the instance you launched. You can find it in the AWS EC2 Instances dashboard, under the `IPv4 Public IP` column.
    
* The first time you connect to the instance, you'll get a message about the authenticity of the host and asked if you want to proceed. Type 'yes' to continue.
    
* Now you should be connected to the instance! You'll see a message about the environments available, and you may see a note asking you to reboot. You can reboot by typing `sudo reboot`, waiting a few minutes and then re-connecting to the instance using the `ssh` command above.

* To double check that you are in the instance, type the following bash command: `ls`. This will list the files/directories in the home directory. If you created the instance using the suggested AMI, you should see the following: `anaconda3 | Nvidia_Clud_EULA.pdf | src | tutorials`.

* NOTE: If you get this far in the module and have to stop, **make sure you STOP your EC2 instance** to avoid being charged continuously. See the last step of this guide. 
## 4. Important: Stopping your EC2 instance.

It's very important to stop your EC2 instance when you are done for the day. Otherwise, you will continue to be charged. 

* Save and close your jupyter notebook (if you have one open).

* [Optional] Move any files you need off the EC2 instance and on to your own machine. 

* Go to your EC2 dashboard in AWS. You'll see that under `Instance State` the EC2 instance you've been working with be listed as `running`. Right click on the word `running` and a menu will pop up. Hover over `Instance State` and you will see a series of options. Most often you will want to either `Stop` or `Start` the instance. **If you select `Terminate`, you will delete the entire instance, and you will have to start all over to set up the instance.** If you select `Stop`, the instance will stop running, but you will see this instance, complete with your programs and files, when you log back in. 

* The next time you log in, go back to your EC2 Dashboard and change the `Instance State` to `Start`. Then you can start from step 3 of this guide to log in to the instance. The key file (the `xxxx.pem`) will be the same, but note that the IP address (in the `IPv4 Public IP` column on your Dashboard) will change every time you stop and restart the instance. 





# Connecting to EC2 with SSH (Secure Shell)

## Windows - Putty
Windows Putty Installation - These instructions are a simplified version of AWS’s Putty Connection instructions for the Ubuntu AMI.
## Step 1 - Download Putty
Download Putty from putty.org. Select the version that matches your version of Windows.
 ![ssh_step1](https://github.com/user-attachments/assets/8d01e6ae-363d-410d-a91d-3c24c18fee2f)

 ## Step 2 - Convert .pem to .ppk with PuttyGen
Open PuttyGen to convert the .pem file to a .ppk file. Load the .pem by navigating to your AWS Keys folder (you make this and store the key here during the Key-Pair step). Once loaded, select “RSA” and click “Save Private Key”. This will generate a .ppk file.
![ssh_step2](https://github.com/user-attachments/assets/2c169eae-1a5a-41a8-972b-76246bb10175)

## Step 3 - Connect to EC2 with Putty
Open Putty to SSH into (connect to) the EC2 Server via the Command Line Interface (CLI).
![ssh_step3](https://github.com/user-attachments/assets/c7fa1326-9026-49e6-a8e0-d342d6f3695c)

## You can find your Public DNS for your EC2 Server here.
![Uploading ssh_step3b.jpg…]()

## Part 2 - Setup the path to your .ppk file
Under Connection > SSH > Auth, add the location of your .ppk file.
![ssh_step4](https://github.com/user-attachments/assets/8cf9fa64-65fa-4406-94aa-c3a3b3bff9d2)
##  Part 3 (Optional) - Save these settings in Putty
![ssh_step3c](https://github.com/user-attachments/assets/8e828ee4-579f-4da0-b84f-8b341e6f1c81)

## Step 4 - Connect to CLI
The Command Line Interface (CLI) to the AWS Ubuntu Server should now open.
![ssh_step5](https://github.com/user-attachments/assets/7b193170-562b-429c-964b-ee911e52507d)

## Mac / Linux - Terminal
 ##  SSH into EC2
 Linux are much easier to set up. Just use the terminal in either RStudio or Terminal App on Mac.

Navigate in your Terminal to the location of your AWS .pem file. Use cd ./Desktop/aws_key.

Run: chmod 400 your_pem_file.pem

![ssh_mac1](https://github.com/user-attachments/assets/067e9434-add9-4ec2-9dfc-ab6238727a1d)

Use the SSH script from the EC2 “Connect” Popup Instructions.

![ssh_mac2](https://github.com/user-attachments/assets/7bd36f52-b97f-4bc8-8cd1-78324bfae2e3)

## (Optional) Add Key (.pem) File to SSH List

You can store your AWS EC2 Key .pem location using ssh-add -K <path-to>/your_key.pem. Then, subsequent reconnects will not need the -i flag.

##  Create a .ppk file (used later with FileZilla)

Sometimes you will need to convert a .pem file to a .ppk file.

Install putty via Terminal: brew install putty
If you don’t have Homebrew installed visit: https://brew.sh/
Navigate to your key directory in your Terminal using cd.
Run: puttygen mykey.pem -o mykey.ppk
Swap the “mykey” file names for your file names


## Docker Overview & Installation
This document covers the basic docker commands used frequently when working with images and containers.


![docker](https://github.com/user-attachments/assets/583cfad1-3a57-477f-ae4e-356be1f0db22)

# What is Docker?
Docker is an amazingly popular software for creating virtual environments that can be used to deploy applications in containerized (controlled) environments.

# How Docker Works

A Shiny App run on AWS EC2 consists of:

1.Files - Controlled by git version control
2.Software Environment - Controlled by docker image


![docker_1](https://github.com/user-attachments/assets/4548dd8a-34f8-49f5-9cdc-8f6acb903ccd)


# Definitions
1.DockerHub - An online community for storing and sharing container images. Has Public and Private repositories for image storage.

2.Container - A container is a virtual environment that combines a Docker Image with software (files) to run an application in a controlled environment (a reproducible software environment created virtually from the Docker Image).

3.Image - An image is an environment that has been built from a series of instructions called a DockerFile. Images can be prebuilt and hosted on DockerHub (similar to how GitHub hosts version controlled software files). The image is needed to run a Docker Container.

# Installation
Install using sudo apt-get install docker.io

sudo apt-get install docker.io
Note: I no longer recommend using sudo snap install docker because the snap version may be out-dated.

##  Docker Command Line Interface (CLI)

#  General
docker --help - Show all docker commands
docker --version - Show the Docker version information - Good for verifying that docker is installed

![docker_help](https://github.com/user-attachments/assets/296fed9a-08f3-475c-995a-de317a429a59)

# DockerHub

DockerHub (https://hub.docker.com/) contains many pre-built images that other users and organizations have created.

These commands are useful for connecting to a remote Docker registry to download docker images.

login - Log in to a Docker registry
logout - Log out from a Docker registry
search - Search the Docker Hub for images
push - Push an image or a repository to a registry
pull - Pull an image or a repository from a registry
commit - Create a new image from a container’s changes

 # Containers
Use docker container --help to list all “container management” commands. These are the common commands.

run - Run a command in a new container
ps - List running containers (I use docker container ls, which does the same thing)
stop - Stop one or more running containers
start - Start one or more stopped containers
rm - Remove one or more containers (I use docker container rm, which does the same thing)

# Images
Use docker image --help to list “image management” commands.

build - Build an image from a Dockerfile
tag - Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE
images - List images (I use docker image ls, which does the same thing)
rmi - Remove one or more images (I use docker image rm, which does the same thing)



##  Docker Hub

This document covers the using Docker Hub (https://hub.docker.com/) to find images that will help as a starting point for the software container that will run your application.

# Create an Account
Create an account at Docker Hub (https://hub.docker.com/) to be able to push and pull Docker Hub images.


![docker_hub](https://github.com/user-attachments/assets/05e17115-ec88-4a02-a04e-4c93ee41a5d7)

# Log Into Docker via Command Line
Use docker login to log into DockerHub via the commandline.

docker login --help - Use this to see the options for logging in
docker login -u your_user_name - The -u option allows us to pass our user name.
Password - The prompt will request our password for DockerHub


![docker_login](https://github.com/user-attachments/assets/b38fea5a-264d-4b90-8dc1-e20c6dc69c51)

# Searching Docker Hub
Use docker search to search for containers related to our project. Alternatively we can search on the DockerHub website.

docker search shiny - Searches all instances where “shiny” is present. Sorts by DockerHub Stars.
NAME - Docker registry name
STARS - Docker Hub Star count



![docker_search](https://github.com/user-attachments/assets/9ae82110-6e3d-473f-b8d3-67b377b9af3a)

# Searching Docker Hub
Use docker search to search for containers related to our project. Alternatively we can search on the DockerHub website.

docker search shiny - Searches all instances where “shiny” is present. Sorts by DockerHub Stars.
NAME - Docker registry name
STARS - Docker Hub Star count


![docker_pull](https://github.com/user-attachments/assets/1e6666de-9f93-4581-8f4e-cc55074709f9)

# List the Docker Images
docker image ls - List the local docker images installed on the EC2 Server.
Note that you will likely have fewer images
Size - If you install many images, you can quickly eat up server space

![docker_image_ls](https://github.com/user-attachments/assets/d31a27d2-8548-4623-a302-ecd5ee0614bb)


# Wrapup
Now you have the following Docker Images successfully installed on your EC2 Server:

rocker/shiny-verse - Contains shiny server and the tidyverse R packages
rocker/tidyverse - Contains rstudio server and the tidyverse R packages
