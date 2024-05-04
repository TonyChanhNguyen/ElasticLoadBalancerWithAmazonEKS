---
title : "Create basic application"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 2.4 </b> "
---
In this step, we will create a basic application using NodeJS and Express framework.

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
![Create basic application](../../images/2.prerequisites/2.4.createapp/2.4.1.createapp.png?pc=60pt)
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

6. Save the file and enter this command in terminal to run the application.
```
node index.js
```

7. But you will get the error below.
![Create basic application](../../images/2.prerequisites/2.4.createapp/2.4.2.createapp.png?pc=60pt)

9. To fix this error, open **package.json** file and add the definition below. Then save it.
```
"type":"module",
```
![Create basic application](../../images/2.prerequisites/2.4.createapp/2.4.3.createapp.png?pc=60pt)

10. Now, let enter the command to run your application again.
```
node index.js
```

11. See, your application ran on port 8080.
![Create basic application](../../images/2.prerequisites/2.4.createapp/2.4.4.createapp.png?pc=60pt)

Now, we need to access to application to see the result.

12. Click on **Share**.
13. Copy the **IP Address** at **Application** field.
![Create basic application](../../images/2.prerequisites/2.4.createapp/2.4.5.createapp.png?pc=60pt)

14. Access to the application with URL ```http://<REPLACE_YOUR_IP>:8080```.
15. Opps, the application can not be accessed.
![Create basic application](../../images/2.prerequisites/2.4.createapp/2.4.6.createapp.png?pc=60pt)

The reason is the security group of workspace instance is still not opened for port 8080.

16. Click to **R** symbol and choose **Manage EC2 Instance** to go back to workspace instance.
![Create basic application](../../images/2.prerequisites/2.4.createapp/2.4.7.createapp.png?pc=60pt)

17. At **Instances** page, select your workspace instance.
18. Navigate to **Security** tab.
19. Click to **Security Group**.
![Create basic application](../../images/2.prerequisites/2.4.createapp/2.4.8.createapp.png?pc=60pt)

At **Inbound rules** interface, you can see there are no rules were defined.

20. Click on **Edit inbound rules**.
![Create basic application](../../images/2.prerequisites/2.4.createapp/2.4.9.createapp.png?pc=60pt)
21. At **Edit inbound rules** interface, click on **Add rule**.
![Create basic application](../../images/2.prerequisites/2.4.createapp/2.4.10.createapp.png?pc=60pt)
22. Define the rule with parameter:
+ **Type** is **Custom TCP**.
+ **Port range** is **8080**.
+ **Source** is **Anywhere-IPv4**.

23. Then, click on **Save rules**.
![Create basic application](../../images/2.prerequisites/2.4.createapp/2.4.11.createapp.png?pc=60pt)

24. Now, let access to your application again and see the result.
![Create basic application](../../images/2.prerequisites/2.4.createapp/2.4.12.createapp.png?pc=60pt)

#### Congratulation, it works.