---
title : "Create Application"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 2.5 </b> "
---

In this section, we will clone a sample **FCJ Management**  application.

### Clone application.
1. Check the Git version of your workspace.
```
git version
```
![Create Application](../../images/2.prerequisites/2.5.createapp/2.5.1.createapp.png?pc=60pt)

2. Upgrade Git to latest version.
```
sudo yum update git
```
![Create Application](../../images/2.prerequisites/2.5.createapp/2.5.2.createapp.png?pc=60pt)

3. Create and move to working directory.
```
mkdir fcj-user-management
cd fcj-user-management
```
![Create Application](../../images/2.prerequisites/2.5.createapp/2.5.3.createapp.png?pc=60pt)

4. Clone application to your workspace.
```
git clone https://github.com/First-Cloud-Journey/000004-EC2.git
```
![Create Application](../../images/2.prerequisites/2.5.createapp/2.5.4.createapp.png?pc=60pt)

5. List all resources of application.
```
ls
ls 000004-EC2
```
![Create Application](../../images/2.prerequisites/2.5.createapp/2.5.5.createapp.png?pc=60pt)

6. Rename folder **000004-EC2** to **Application**.
```
mv 000004-EC2 Application
```
![Create Application](../../images/2.prerequisites/2.5.createapp/2.5.6.createapp.png?pc=60pt)

7. List all resources of application again.
```
ls
ls Application
```
![Create Application](../../images/2.prerequisites/2.5.createapp/2.5.7.createapp.png?pc=60pt)
8. Move to application directory and install dependencies of application on **package.json**.
```
cd Application
npm install
```
![Create Application](../../images/2.prerequisites/2.5.createapp/2.5.8.createapp.png?pc=60pt)
9. Start application.
```
node app.js
```
![Create Application](../../images/2.prerequisites/2.5.createapp/2.5.9.createapp.png?pc=60pt)

The application will get error **Fail to connect to database**, since there are no MySQL database created and provided for application to use.

Now, we will containerize the application to Container Image by Docker. The MySQL Database will be provided in next sections.

### Containerize application
1. Check the version of Docker.
```
docker version
```
![Create Application](../../images/2.prerequisites/2.5.createapp/2.5.10.createapp.png?pc=60pt)

2. Create a file name **Dockerfile**.
```
touch Dockerfile
```
![Create Application](../../images/2.prerequisites/2.5.createapp/2.5.11.createapp.png?pc=60pt)

3. Open **Dockerfile** file, paste the code below and save.
```cmd
# Use an official Node.js runtime as a parent image
FROM node:13-alpine
# Set the working directory in the container
WORKDIR app
# Copy package.json and package-lock.json to the working directory
COPY package*.json ./
# Install dependencies
RUN npm install
# Copy the rest of the application code
COPY . .
# Expose the port the app runs on
EXPOSE 5000
# Command to run the application
CMD ["npm","start"]
```
![Create Application](../../images/2.prerequisites/2.5.createapp/2.5.12.createapp.png?pc=60pt)

4. Create a file name **.dockerignore** to mitigate capacity for container image.
```
touch .dockerignore
```
![Create Application](../../images/2.prerequisites/2.5.createapp/2.5.13.createapp.png?pc=60pt)

5. Click on **Settings** symbol, select **Show Hidden Files** to see the hidden file **.dockerignore**.
![Create Application](../../images/2.prerequisites/2.5.createapp/2.5.14.createapp.png?pc=60pt)

6. Open **.dockerignore** file, paste the code below and save.
```
.git
node_modules
Dockerfile
```
![Create Application](../../images/2.prerequisites/2.5.createapp/2.5.15.createapp.png?pc=60pt)

7. List all image in your workspace. Make sure there are no image named **fcj-management** since we will use this name for container image.
```
docker images
```

8. Let build your application to container image.
```
docker build -t fcj-management:v1 .
```
![Create Application](../../images/2.prerequisites/2.5.createapp/2.5.16.createapp.png?pc=60pt)

9. After this process finished successfully. Let list the images in your workspace again.
```
docker images
```
![Create Application](../../images/2.prerequisites/2.5.createapp/2.5.17.createapp.png?pc=60pt)

The container image named **fcj-management** with tag **v1** is created successfully from **FCJ Management**  application. Now let use Docker to deploy your application from created container image for testing purpose.

10. Execute the below command to deploy the application from created container image.
```
docker run -d --name testing-application -e PORT=8080 -p 8080:8080 fcj-management:v1
```
![Create Application](../../images/2.prerequisites/2.5.createapp/2.5.18.createapp.png?pc=60pt)

11. List the running process in your workspace.
```
docker ps
```
![Create Application](../../images/2.prerequisites/2.5.createapp/2.5.20.createapp.png?pc=60pt)

12. Watching the logs of the application.
```
docker logs testing-application
```
![Create Application](../../images/2.prerequisites/2.5.createapp/2.5.21.createapp.png?pc=60pt)

The errors are shown in log also **Fail to connect to database**. It means the container image is built successfully.

13. Let delete running process.
```
docker stop testing-application
docker rm testing-application
docker ps -a
```
![Create Application](../../images/2.prerequisites/2.5.createapp/2.5.22.createapp.png?pc=60pt)


### Push container image to DockerHub
In this step, we will push the created container image to DockerHub for Amazon EKS Cluster can pull to run Pod. So we will not deep dive how to use DockerHub, you can access to [DockerHub Docs](https://docs.docker.com/) for detail.
1. Log in to [DockerHub](https://hub.docker.com/repository/docker) with your account.
2. Create a new repository named ```fcj-management```.
![Create Application](../../images/2.prerequisites/2.5.createapp/2.5.23.createapp.png?pc=60pt)

3. Go to [DockerHub Security](https://hub.docker.com/settings/security) to create new access token for Cloud9 workspace log in.
4. Click on **New Access Token**.
![Create Application](../../images/2.prerequisites/2.5.createapp/2.5.24.createapp.png?pc=60pt)

5. Input the **Access Token Description** as ```Access Token for FCJ Workshop```.
6. Keep **Access Permissions** as default (with Read, Write, Delete permission).
7. Then, click on **Generate**.
![Create Application](../../images/2.prerequisites/2.5.createapp/2.5.25.createapp.png?pc=60pt)

8. Save your access token to use later.
![Create Application](../../images/2.prerequisites/2.5.createapp/2.5.26.createapp.png?pc=60pt)

9. Back to Cloud9 terminal, log in to DockerHub.
```
docker login -u firstcloudjourneypcr
```

10. Enter your access token when be asked **Password**.
![Create Application](../../images/2.prerequisites/2.5.createapp/2.5.27.createapp.png?pc=60pt)


To push your container image to DockerHub repository. The repository of container image must be match with repository name on DockerHub. 

11. To do that, we will tag the container image.
```
docker tag fcj-management:v1 firstcloudjourneypcr/fcj-management:v1
```

![Create Application](../../images/2.prerequisites/2.5.createapp/2.5.28.createapp.png?pc=60pt)

12. List all images on your workspace.
```
docker images
```

![Create Application](../../images/2.prerequisites/2.5.createapp/2.5.29.createapp.png?pc=60pt)

There is a new duplicated image with repository named **firstcloudjourneypcr/fcj-management**, tag is **v1**.

13. Now, let push the container image to DockerHub.
```
docker push firstcloudjourneypcr/fcj-management:v1
```

![Create Application](../../images/2.prerequisites/2.5.createapp/2.5.30.createapp.png?pc=60pt)

14. Go to DockerHub Repository **firstcloudjourneypcr/fcj-management** to see the result.

![Create Application](../../images/2.prerequisites/2.5.createapp/2.5.31.createapp.png?pc=60pt)

There is a new pushed image on your DockerHub repository with tag is **v1**.