# **Building a Serverless Web Application with AWS S3, Lambda, DynamoDB, CloudFront, and AWS WAF**

## Introduction
Serverless architecture has revolutionized the way we build and deploy web applications. By abstracting away server management and scaling, it allows developers to focus on writing code and delivering value to users. In this article, we will explore the world of **serverless web applications** using a powerful combination of AWS services. We will leverage **AWS S3** for hosting, **Lambda functions** for data retrieval and storage, **DynamoDB** as a scalable database, **API Gateway** for creating RESTful APIs, **CloudFront** for global content delivery, and **AWS WAF** to secure against SQL injections. Join us on this journey as we dive into the implementation details, best practices, and benefits of building a serverless web application using AWS services.

![diagram](./images/diagram.png)

## Overview of serverless architecture
Serverless architecture is a cloud computing paradigm that eliminates the need for managing servers and infrastructure. In this model, the cloud provider takes care of server provisioning, scaling, and maintenance, allowing developers to focus solely on writing code. Applications are built using small, stateless functions that are triggered by events. These functions are executed in a managed environment and automatically scale based on demand. Serverless architecture offers benefits such as cost efficiency, scalability, reduced operational overhead, and rapid development.

## Introduction to the AWS services used in the article
To build a serverless web application, we leverage several key AWS services. Firstly, Amazon S3 (Simple Storage Service) provides us with a highly scalable and durable storage solution for hosting our web application files. We utilize AWS Lambda, a serverless compute service, to write functions that retrieve and store data, ensuring efficient and on-demand processing. DynamoDB, a fully managed NoSQL database, serves as our data storage solution, offering scalability and low latency access. AWS API Gateway enables us to create RESTful APIs to expose our Lambda functions securely. CloudFront, AWS's content delivery network (CDN), ensures fast and reliable global content distribution of our web application. Lastly, AWS WAF (Web Application Firewall) adds a layer of security by protecting against SQL injections and other web attacks. Together, these AWS services provide a comprehensive infrastructure for building a secure and scalable serverless web application.

## Prerequisites

Before diving into building a serverless web application with AWS services, it is essential to have a few prerequisites in place. 

- **AWS Account**: Create an AWS account to access and utilize the AWS services mentioned in this article.
- **AWS IAM**: Understand the basics of AWS Identity and Access Management (IAM) for managing user permissions and roles within the AWS environment.

## STEP 1: Configure AWS S3, CloudFront, and WAF

### Setting up an AWS S3 bucket for hosting the web application
After logging in to the AWS Management Console and navigating to the Amazon S3 service, you will find the option to create a new S3 bucket. Clicking on **Create Bucket** will initiate the process of setting up your bucket for hosting the web application.
![1](./images/1.png)

You will be prompted to provide a unique name for your bucket. It's important to choose a name that is globally unique within the entire AWS S3 namespace. This ensures that there are no naming conflicts with existing buckets. Select the region where you want to host your web application.
![2](./images/2.png)
Then click on create bucket

Once the bucket is created, you can proceed to upload your web application files. Click on your bucket and select the "Upload" option then "Add files". 
![4](./images/4.png)

Choose the files from your local machine that make up your web application. After having add your files go down and click on upload button 
![6](./images/6.png)


### Setting up AWS WAF rules and conditions
On the AWS Management Console and navigate to the AWS WAF service
![waf](./images/7.png)
Click on create web ACL
![waf](./images/8.png)
Provide a name for your web ACL and select ressource type in our case **Amazon cloudFront distributions**. Then click on **Next**
In the section **Add rules and rule groups** 
![rules](./images/9.png) 
click on Add rules then select Add managed rule groups
![rules](./images/10.png) 
In the next window, expand "AWS Managed rule groups" and scroll down to **Free rule groups**. Add a few rules to protect your application.

![rules](./images/11.png) 
Scroll down and click on "Add rules."
Ensure that requests not matching any rules are Allowed. To do this, select "Allow" under the "Default action" section.
![rules](./images/12.png) 
Click on "Next" until you reach the end, leaving the default settings as they are. Finally, review your configurations and click on "Create web ACL."
Our web ACL has been created 
![rules](./images/13.png) 

### Setting up CloudFront distribution for the S3 bucket

On the AWS Management Console and navigate to the AWS CloudFront service
![cloudFront](./images/14.png) 
Click on "Create Distribution" to create a CloudFront distribution.
In the Create Distribution interface, click on the "Origin Domain" field and select the previously created S3 bucket.
![cloudFront](./images/15.png) 

Under "Origin Access," select "Origin access control setting."

![cloudFront](./images/16.png) 
Then, click on "Create control setting Identity" and leave all the settings at their default values. Click on "Create" to proceed.
![cloudFront](./images/17.png) 

At viewer protocol policy
Apply the same settings as shown in the screenshot below.
![cloudFront](./images/18.png) 
In the next section, apply the following configurations, and then select the previously created web ACL
![cloudFront](./images/19.png) 
In the "Default Root Object" section, enter the name of your main HTML file, in our case, "index.html."
![cloudFront](./images/23.png) 
Then, click on "Create." After the new distribution is created, click on "Copy Policy."

![cloudFront](./images/20.png) 
This policy will grant CloudFront access to our S3 bucket. To do this, go back to the S3 service and select the bucket containing our website. Then, go to "Permissions."

![cloudFront](./images/21.png) 
In the "Bucket Policy" section, click on "Edit," delete the existing content, paste the copied policy, and click on "Save Changes."
![cloudFront](./images/22.png) 

![cloudFront](./images/24.png) 
Wait a few minutes for your distribution to deploy, then copy the domain name and paste it into your browser.
![cloudFront](./images/25.png) 

## STEP 2: Configure DynamoDB and Lambda Functions

### Creating an AWS DynamoDB table for data storage
Navigate to the DynamoDB service

![dynamoDB](./images/26.png) 
Then, click on "Create table." 

![dynamoDB](./images/27.png) 
Provide a name for your table and specify the partition key value. Leave all other parameters at their default settings.

Your table has been successfully created.
![dynamoDB](./images/28.png) 

### Configuring IAM roles and permissions for Lambda functions
Navigate to the IAM service
![Role](./images/29.png) 

In the left sidebar of the IAM service, click on "Roles." In the new interface, click on "Create role." 
![Role](./images/30.png) 

In the new interface, select the Lambda service and click on "Next."
![Role](./images/31.png) 

In the search results, find the DynamoDB permissions, select the "AmazonDynamoDBFullAccess" policy, and click on "Next." 
![Role](./images/32.png) 

In the new interface, enter the role name, verify the policy selection, and click on "Create role."
![Role](./images/33.png) 

### Create Lambda Functions 

Navigate to the Lambda service and clic on create a function
![Lambda](./images/34.png) 

Enter the function name and select "Python 3.9" as the runtime.
![Lambda](./images/35.png) 

In the permissions section, select "Use an existing role" and choose the previously created role. Then, click on "Create function."
![Lambda](./images/36.png) 

After creating the function, in the new window, scroll down and select the "Code" tab. Enter your code in this section and then click on "Deploy."
![Lambda](./images/37.png) 

Our Lambda function has been successfully created.
![Lambda](./images/38.png) 

Repeat the same steps to create the other functions.
Function: getEmployees
![Lambda](./images/39.png) 

Remember to deploy your functions after creating them.

## STEP 3: Implementing API Gateway

Navigate to the API Gateway service

![apigateway](./images/40.png)

Scroll down to the "REST API" section and click on "Build."

![apigateway](./images/41.png)

Select "New API" and provide the required information. Then, click on "Create API."

![apigateway](./images/42.png)

In the new window, click on "Action" and then "Create Method."

![apigateway](./images/43.png)

Let's start by creating a method to retrieve employees saved in our DynamoDB table. Select the GET method, enter the name of the previously created Lambda function, and click on "Save."
![apigateway](./images/44.png)

For the insertEmployee function, create a method of type POST. 
![apigateway](./images/45.png)

With our methods ready, let's deploy our API. Click on "Action," then "Deploy API." In the popup window, select "New stage" and enter the stage name. Then, deploy.
![apigateway](./images/46.png)

![apigateway](./images/47.png)

Our API has been successfully deployed. Go back to the Resources tab, click on "Action," and then "Enable CORS."
Since our API Gateway will interact with CloudFront, and these two services are not in the same domain, we have enabled CORS to avoid CORS origin errors. Redeploy your API.

![apigateway](./images/48.png)

Now, copy the address of your API. 
![apigateway](./images/49.png)

Go to the source code of your application, specifically the scripts.js file. Modify the API_ENDPOINT variable and enter the address of your API.
![apigateway](./images/50.png)

## STEP 4: Testing the Application

go to CloudFront service and copy the url and paste it in your browser

![apigateway](./images/51.png)

![apigateway](./images/52.png)

## Conclusion

In this article, we explored the process of building a serverless web application using AWS services. We began by configuring AWS S3 for hosting the application, leveraging CloudFront for content delivery, and implementing AWS WAF for security against SQL injections. Next, we set up DynamoDB as our data storage solution and configured Lambda functions to retrieve and store data. Additionally, we implemented API Gateway to expose our Lambda functions as RESTful API endpoints. Finally, we tested the application's functionality, including API endpoints and data retrieval/storage.

By harnessing the power of serverless architecture and AWS services, we achieved a scalable, secure, and efficient web application deployment. The combination of S3, CloudFront, DynamoDB, Lambda, and API Gateway provided a robust infrastructure for our serverless web application, enabling seamless integration and optimal performance. With these tools and techniques, you can build and deploy your own serverless web applications with confidence, leveraging the benefits of cloud computing and focusing on delivering exceptional user experiences.
