# Step-by-step Guide to Scaling Docker with AWS ECS Fargate and Load Balancing
In modern DevOps workflows, applications must be highly available, scalable, and resilient, able to handle unpredictable workloads and traffic flows. Running a single container or server can quickly become insufficient when traffic increases or failures occur. To address this, AWS provides Elastic Container Service (ECS) , a fully managed container orchestration service that makes it easy to run, stop, and manage Docker containers on a cluster. With Fargate, allowing containers to run without managing underlying servers. When combined with an Application Load Balancer (ALB), ECS ensures that incoming requests are automatically distributed across multiple healthy containers, allowing high availability and scalability.
This comprehensive step-by-step guide walks you through the process of Scaling Docker with AWS ECS Fargate and Load Balancing. This involves deploying a Dockerized application to ECS Fargate, configuring it behind an ALB, and enabling scaling for high availability and performance. The aim of this lab is to learn : 
What container orchestration is and why it's needed.
How to use AWS ECS (Fargate launch type) to run Docker containers.
How to define ECS Task Definitions and Services.
How to provision an Application Load Balancer (ALB) to distribute traffic.
How to use Terraform to deploy a highly available and scalable Docker application.
Prerequisites
Have an AWS Account with sufficient permissions.
Have Terraform installed and configured with AWS credentials.
Have completed previous Docker Labs.
Have docker-compose-app code in a GitHub repository.
Set up an AWS ECR repository with your Flask app Docker image pushed by CodeBuild.
Step 1: Update Terraform Configuration for ECS and ALB
In this lab, we will be defining a new set of AWS resources : a Virtual Private Cloud (VPC) with subnets, an ECS cluster, an ECS Task Definition (describing our Docker container), an ECS Service (running our task), and an Application Load Balancer (ALB) to route traffic. To complete Step 1, follow the instructions below : 
Open your aws-docker-infra/main.tf file.
update the terraform configuration file main.tf to define a new VPC and security groups dedicated to ECS : remove the aws_instance, its related user_data block from Lab 5, the aws_security_group.allow_ssh and aws_vpc.main. As we will no longer be using a single EC2 instance directly. Add the following resources to your main.tf file. This will replace the EC2 instance setup with an ECS Fargate setup.









Step 2: Apply Terraform Changes for ECS and ALB
We have updated the terraform configuration files to create a VPC, subnets, security groups, ECS cluster, task definition, service, and the Application Load Balancer. Now it's time to provision all these new cloud resources using Terraform. To complete Step 2, follow the instructions below : 
Open your command line or terminal and navigate to your aws-docker-infra folder. 
Run terraform init again to ensure all new provider requirements are met. 
Run terraform plan to review the extensive list of resources that will be created. This will be a much larger plan than before! 
Run terraform apply, if the plan is correct, to provision these resources. Type yes when prompted.









5. Check your infrastructure on the AWS Console. The VPC was created as well as its components.
VPC with subnets


NACL

Security groups


ECS cluster


ECS Task Definition that describes our Docker container


ECS Service that runs our task


Application Load Balancer (ALB) to route traffic.


ALB’s target group


6. Set up manually a Postgresql Database on AWS Console to connect it to the flask-app.

Set up a private subnet within the VPC, private route tables and a NAT gateway.

Subnet : Go to the VPC dashboard → Subnets → Create subnet  → Select our VPC → specify a name for the subnet, a CIDR → Click Create subnet.



Route table : Go to the VPC dashboard → Route Tables → Create route table → specify a name for the RT → Select our VPC → Click Create route table.


NAT Gateway : Go to the VPC dashboard → NAT Gateways → Create NAT Gateway → specify a name for the NAT Gateway → Select our public subnet → specify connectivity type → Click Create NAT Gateway.


Update Subnet and routes tables

Update Subnet : Go to the VPC dashboard → Route Tables → Select your route table → Actions → Edit subnet associations → Select the subnet to associate → Click Save associations.


Update Routes Tables : Go to the VPC dashboard → Route Tables → Select your route table → Actions → Edit routes → Add route → Specify Destination 0.0.0.0/0, specify target Nat Gateway and nat gateway name → Click Save changes.


Configure a security group for the database.

Go to the VPC dashboard → Security → Security groups → Create security group → specify Basic Settings (name, description, select your VPC) → Add inbound rule to allow PostgreSQL traffic on port 5432 → leave outbound rule setting default → Click Create security group.




Create the database and associate it to the private subnet and security group within the VPC

Go to the RDS and Aurora dashboard → Click Create Database → Define settings ( standard create, PostgreSQL, Production or Dev / Test environment, availability and durability, database name, master username, your VPC, DB subnet group, attach your security group) → Click Create database.



Replace the database endpoint by the actual one in the main.tf terraform file


Run terraform apply to update the configuration and allow the application to connect to the database.








Step 3: Access Your Scalable Application
Your application is now running on AWS ECS Fargate, behind an Application Load Balancer. This means traffic is distributed across multiple instances of your Flask app, providing high availability. To complete Step 3, follow the instructions below : 
Open your web browser.
In the address bar, type the alb_dns_name you got from the terraform apply output and Press Enter.

Error 1 : 503 Service Temporarily Unavailable
The Application is not reachable with the ALB DNS ! This error means that the server is not ready to handle the request. Let's troubleshoot it step-by-step.

Troubleshooting Error 1 : 
Step 1 : To troubleshoot this error we will first check the task status and see if they are running or not. 
Go to ECR → Clusters → my-flask-app-cluster → Tasks → Last status : all the tasks are stopped. This means that the application is not running, the containers keep crashing and draining. 


Step 2 : The tasks are stopped. Let's trigger a build and check the build logs. This will tell us the reason why the containers keeps crashing. 

Build Logs : this error means that the AWS CodeBuild service role, when running the build, does not have permission to read the secret from AWS Secrets Manager. 
Let’s fix this error and update the terraform configuration file !





Step 3 : Now that we have granted the AWS code build the required permissions to have the build triggered successfully lets check the application from the web browser using the ALB DNS ! → error 2 : Database connection failed. 
	

Error 2 : Database connection failed
The flask application is not reachable due to database connection failure. The database "mydatabase" does not exist, which causes failure toLet’s properly configure the database before reaching the application !
Troubleshooting Error 2 : 
Step 1 : Check the Application logs with CloudWatch. 
Go to CloudWatch → Log groups → ecs/my-flask-app → log events.
Error : HTTP status code returned by Flask. 500 = Internal Server Error this means that something went wrong inside the server, causing ECS tasks to be marked unhealthy.


Step 2 : Set up a Bastion Host Server within the same VPC.
Go to EC2 Dashboard → Instances → Launch Instance → specify the instance settings (OS, VPC → select same VPC as your RDS, subnet → select public subnet, auto assign public IP) → specify security group → allow inbound SSH traffic on port 22 and all outbound traffic. )

Step 3 : Configure Networking 
ECS Task Security Group : allow outbound rule PostgresSQL traffic on port 5432.

RDS Security Group : allow inbound rule PostgresSQL on port 5432 from the bastion host security group and the ECS Task security group.

Bastion Host Server Security Group : allow outbound rule all traffic and PostgreSQL on port 5432. 

NACL  : allow inbound and outbound rules all traffic and inbound rule PostgresSQL on port 5432 from 0.0.0.0/0


Step 4 : Connect to the Bastion host server using SSH, install postgresql and update the package manager.

Step 5 : Verify the DB connectivity 
using the command : psql -h <rds-endpoint> -U <username> -d <database-name>
Step 6 : create the Application Database manually to connect it to the Flask Application !
using the commands : CREATE DATABASE mydatabase;

Step 7 : Access the application 
Now that we have troubleshoot our application let’s access it from our server using the command : curl http://alb_dns_name/ and from the web browser as well using : http://alb_dns_name














You should now see your "Simple Message Board" application running live in the cloud, served by ECS Fargate! Add some messages.


Step 8 : Explore in the AWS Console the Application
Go to the AWS Console and search for "ECS."
Click on "Clusters" and find my-flask-app-cluster.
Go to the "Services" tab and click on my-flask-app-service.
You can see the "Tasks" tab to observe your running tasks (containers). You should see desired_count (2) tasks running. If one fails, ECS will automatically replace it!

Search for "ECR" and verify your image is there.

Search for "EC2" -> "Load Balancers" and find my-flask-app-lb. You can see its DNS name there too.

Step 4 : Clean Up
Cleaning up the infrastructure is a critical step. It allows to destroy all AWS resources created in this lab to avoid incurring significant ongoing costs. Load balancers, ECS services, and network components can be expensive if left running.
Go back to your local terminal.
Navigate to your aws-docker-infra folder. Run the Terraform destroy command: terraform destroy. Type yes and press Enter to confirm.
This process will take a long time as Terraform systematically tears down the ALB, ECS service, task definitions, cluster, security groups, subnets, VPC, and all associated components. Wait for "Destroy complete!" message. This ensures you don't leave resources running and incur costs.
















Summary
This breakdown provides a step-by-step guide to Scaling Docker with AWS ECS Fargate and Load Balancing. By completing this lab, we had an overview on : 
What container orchestration is and why it's needed.
How to use AWS ECS (Fargate launch type) to run Docker containers.
How to define ECS Task Definitions and Services.
How to provision an Application Load Balancer (ALB) to distribute traffic.
How to use Terraform to deploy a highly available and scalable Docker application.
Scaling Docker with AWS ECS Fargate and Load Balancing is a powerful approach to deploy modern applications. By combining those tools, we have demonstrated how container orchestration, serverless compute, and load balancing work seamlessly together to deliver a robust, production-ready environment on AWS, ensuring fault tolerance, high availability and scalability for varying workloads without downtime.

