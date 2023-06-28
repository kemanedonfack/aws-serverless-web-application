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

