---
title : "Create Application"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 2.5 </b> "
---

In this section, we will create a simple application with two versions V1 and V2. Then, dockerize and push them to DockerHub for EKS Cluster use.

### Create application v1
1. At the Cloud9 terminal, enter the command below to create a new directory for application.
```
mkdir app
cd app
```
2. Initialize application.
```
npm init
```
3. Press **Enter** to skip those step and confirm **Yes** to finish.
![Create basic application](../../images/2.prerequisites/2.5.createapp/2.4.1.createapp.png?pc=60pt)
4. Create a file name **index.js**.
```
touch index.js
```
5. Open **index.js** file and perform code.
```javascript
import express from 'express'

const app = express()

app.get('/',(req, res) => {
    res.json("Hello world from FCJ Workshop V1!")
})

app.listen(8080, ()=> {
    console.log("application running on 8080")
})
```

6. At Cloud9 Terminal, let install **express** framework.
```
npm i express
```
![Create basic application](../../images/2.prerequisites/2.5.createapp/2.4.1.1.createapp.png?pc=60pt)

7. Save the file and enter this command in terminal to run the application.
```
node index.js
```

8. But you will get the error below.
![Create basic application](../../images/2.prerequisites/2.5.createapp/2.4.2.createapp.png?pc=60pt)

9. To fix this error, open **package.json** file and add the definition below. Then save it.
```
"type":"module",
```
![Create basic application](../../images/2.prerequisites/2.5.createapp/2.4.3.createapp.png?pc=60pt)

10. Now, let enter the command to run your application again.
```
node index.js
```

11. See, your application ran on port 8080.
![Create basic application](../../images/2.prerequisites/2.5.createapp/2.4.4.createapp.png?pc=60pt)

Now, we need to access to application to see the result.

12. Click on **Share**.
13. Copy the **IP Address** at **Application** field.
![Create basic application](../../images/2.prerequisites/2.5.createapp/2.4.5.createapp.png?pc=60pt)

14. Access to the application with URL ```http://<REPLACE_YOUR_IP>:8080```.
15. Opps, the application can not be accessed.
![Create basic application](../../images/2.prerequisites/2.5.createapp/2.4.6.createapp.png?pc=60pt)

The reason is the security group of workspace instance is still not opened for port 8080.

16. Click to **R** symbol and choose **Manage EC2 Instance** to go back to workspace instance.
![Create basic application](../../images/2.prerequisites/2.5.createapp/2.4.7.createapp.png?pc=60pt)

17. At **Instances** page, select your workspace instance.
18. Navigate to **Security** tab.
19. Click to **Security Group**.
![Create basic application](../../images/2.prerequisites/2.5.createapp/2.4.8.createapp.png?pc=60pt)

At **Inbound rules** interface, you can see there are no rules were defined.

20. Click on **Edit inbound rules**.
![Create basic application](../../images/2.prerequisites/2.5.createapp/2.4.9.createapp.png?pc=60pt)
21. At **Edit inbound rules** interface, click on **Add rule**.
![Create basic application](../../images/2.prerequisites/2.5.createapp/2.4.10.createapp.png?pc=60pt)
22. Define the rule with parameter:
+ **Type** is **Custom TCP**.
+ **Port range** is **8080**.
+ **Source** is **Anywhere-IPv4**.

23. Then, click on **Save rules**.
![Create basic application](../../images/2.prerequisites/2.5.createapp/2.4.11.createapp.png?pc=60pt)

24. Now, let access to your application again and see the result.
![Create basic application](../../images/2.prerequisites/2.5.createapp/2.4.12.createapp.png?pc=60pt)

### Dockerize application v1
1. Create a file named **Dockerfile**.
```
touch Dockerfile
```
![Dockerize application](../../images/2.prerequisites/2.5.createapp/3.2.1.createdockerfile.png?pc=90pt)

2. Open the **Dockerfile**.
![Dockerize application](../../images/2.prerequisites/2.5.createapp/3.2.2.createdockerfile.png?pc=90pt)

3. Enter the code below.
```Dockerfile
FROM node:13-alpine

#configure working directory
WORKDIR /app

COPY package.json ./

RUN npm install

#bundle the source code

COPY . ./

EXPOSE 8080

CMD ["node","index.js"]


```
![Dockerize application](../../images/2.prerequisites/2.5.createapp/3.2.3.createdockerfile.png?pc=90pt)

*Note*: 
+ At step **COPY package.json ./**, Docker will copy **package.json** file to working directory **/app**, then process step **RUN npm install** to install all dependencies were defined in **package.json** file and save them to **node_modules** folder.

+ At step **COPY . ./**, Docker will copy all resource to working directory **/app** - include **node_modules** folder and **Dockerfile** file. Those folders and files are not necessary to copy again to working directory. So you can create **.dockerignore** to list which file or folder do not need to copy to working directory.

4. Create file named **.dockerignore** by command.
```
touch .dockerignore
```
![Dockerize application](../../images/2.prerequisites/2.5.createapp/3.2.4.createdockerfile.png?pc=80pt)

You will not see where **.dockerignore** is, since Cloud9 recognize **.dockerignore** is hidden file.

5. Click on **Setting**.
6. Select **Show Hidden Files**.

![Dockerize application](../../images/2.prerequisites/2.5.createapp/3.2.5.createdockerfile.png?pc=90pt)

7. Open **.dockerignore** and enter those values.
```
node_modules
Dockerfile
```
![Dockerize application](../../images/2.prerequisites/2.5.createapp/3.2.6.createdockerfile.png?pc=90pt)

8. Enter this command to list all container images in your machine.
```
docker images
```
![Dockerize application](../../images/2.prerequisites/2.5.createapp/3.2.7.createdockerfile.png?pc=90pt)

There is no existing image in workspace.
9. Let build container image.
```
docker build -t fcj-application:v1 .
```
![Dockerize application](../../images/2.prerequisites/2.5.createapp/3.2.8.createdockerfile.png?pc=90pt)

10. List existing images again.
```
docker images
```
![Dockerize application](../../images/2.prerequisites/2.5.createapp/3.2.9.createdockerfile.png?pc=90pt)

The container image of your application had been built with version **v1** successfully.

### Create application v2
1. Open **index.js** file and replace **res.json("Hello world from FCJ Workshop V1!")** to **res.json("Hello world from FCJ Workshop V2!")** at line 6.
![Create application](../../images/2.prerequisites/2.5.createapp/3.2.10.createdockerfile.png?pc=90pt)

2. Start your application to see the result
![Create application](../../images/2.prerequisites/2.5.createapp/3.2.11.createdockerfile.png?pc=90pt)

3. Access to your application URL again to verify the result.
![Create application](../../images/2.prerequisites/2.5.createapp/3.2.12.createdockerfile.png?pc=90pt)

### Dockerize application v2
1. Let build container image.
```
docker build -t fcj-application:v2 .
```
![Create application](../../images/2.prerequisites/2.5.createapp/3.2.13.createdockerfile.png?pc=90pt)

2. List existing images again.
```
docker images
```
![Create application](../../images/2.prerequisites/2.5.createapp/3.2.14.createdockerfile.png?pc=90pt)

### Push container image to DockerHub.
1. Access and sign in to [DockerHub](https://hub.docker.com/) with your account.
2. Let create a repository for this workshop.
![Create application](../../images/2.prerequisites/2.5.createapp/3.2.15.createdockerfile.png?pc=90pt)

3. Input requested information:
+ **Name**: ```fcj-elbeks-workshop-basicapp```.
+ **Description**: ```Store container image for Amazon ELB with Amazon EKS Cluster Workshop```.
Then click on **Create**.
![Create application](../../images/2.prerequisites/2.5.createapp/3.2.16.createdockerfile.png?pc=90pt)


Next, we will create an access token used to log in DockerHub from workspace instance.

4. Click on your avatar (on the page top right side).

5. Click on **My Account**.

6. Click on **Security**.

7. Finally, click on **New Access Token**.
![Create application](../../images/2.prerequisites/2.5.createapp/3.2.17.createdockerfile.png?pc=90pt)

8. Fill the **Access Token Description**.
9. Click on **Generate**.
![Create application](../../images/2.prerequisites/2.5.createapp/3.2.18.createdockerfile.png?pc=90pt)

10. Store generated access token to use later.
![Create application](../../images/2.prerequisites/2.5.createapp/3.2.19.createdockerfile.png?pc=90pt)

11. Back to terminal of Cloud9. Enter this command to login and provide password when be requested.
```
docker login -u <REPLACE-YOUR-DOCKERHUB-USERNAME>
```
![Create application](../../images/2.prerequisites/2.5.createapp/3.2.20.createdockerfile.png?pc=90pt)


To push container image to repository, Repository of your container image must match with repository format of DockerHub `<YOUR_USER_NAME>/<YOUR_REPOSITORY_NAME>`. So now we need to use `docker image tag command` to copy and correct repository format of your container image in workspace instance.

12. Execute this command to tag your container image version v1 and v2.
```
docker image tag fcj-application:v1 firstcloudjourneypcr/fcj-elbeks-workshop-basicapp:v1
docker image tag fcj-application:v2 firstcloudjourneypcr/fcj-elbeks-workshop-basicapp:v2
```
![Create application](../../images/2.prerequisites/2.5.createapp/3.2.21.createdockerfile.png?pc=90pt)


13. List all your container images again.
```
docker images
```
![Create application](../../images/2.prerequisites/2.5.createapp/3.2.22.createdockerfile.png?pc=90pt)

Now, there are 2 new container images is firstcloudjourneypcr/fcj-elbeks-workshop-basicapp with tag is v1 and firstcloudjourneypcr/fcj-elbeks-workshop-basicapp with tag is v2. Next, let push those container images to DockerHub.

14. Execute this command to push your container image both version v1 and v2 to DockerHub.
```
docker push firstcloudjourneypcr/fcj-elbeks-workshop-basicapp:v1
docker push firstcloudjourneypcr/fcj-elbeks-workshop-basicapp:v2
```
![Create application](../../images/2.prerequisites/2.5.createapp/3.2.23.createdockerfile.png?pc=90pt)

15. Back to your Docker Hub repository to check the result. There is 2 container images had been pushed to.

![Create application](../../images/2.prerequisites/2.5.createapp/3.2.24.createdockerfile.png?pc=90pt)
