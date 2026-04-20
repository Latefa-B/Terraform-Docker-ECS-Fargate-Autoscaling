# Step-by-step Guide to Scaling Docker with AWS ECS Fargate and Load Balancing
In modern DevOps workflows, applications must be highly available, scalable, and resilient, able to handle unpredictable workloads and traffic flows. Running a single container or server can quickly become insufficient when traffic increases or failures occur. To address this, AWS provides Elastic Container Service (ECS) , a fully managed container orchestration service that makes it easy to run, stop, and manage Docker containers on a cluster. With Fargate, allowing containers to run without managing underlying servers. When combined with an Application Load Balancer (ALB), ECS ensures that incoming requests are automatically distributed across multiple healthy containers, allowing high availability and scalability.

This comprehensive step-by-step guide walks you through the process of Scaling Docker with AWS ECS Fargate and Load Balancing. This involves deploying a Dockerized application to ECS Fargate, configuring it behind an ALB, and enabling scaling for high availability and performance. The aim of this project is to learn : 

- What container orchestration is and why it's needed.
- How to use AWS ECS (Fargate launch type) to run Docker containers.
- How to define ECS Task Definitions and Services.
- How to provision an Application Load Balancer (ALB) to distribute traffic.
- How to use Terraform to deploy a highly available and scalable Docker application.

## Prerequisites
- Have an AWS Account with sufficient permissions.
- Have Terraform installed and configured with AWS credentials.
- Have completed previous Docker Labs.
- Have docker-compose-app code in a GitHub repository.
- Set up an AWS ECR repository with your Flask app Docker image pushed by CodeBuild.

## Step-by-step instructions:
### Step 1: Update Terraform Configuration for ECS and ALB
In this project, we will be defining a new set of AWS resources : a Virtual Private Cloud (VPC) with subnets, an ECS cluster, an ECS Task Definition (describing our Docker container), an ECS Service (running our task), and an Application Load Balancer (ALB) to route traffic. To complete Step 1, follow the instructions below : 
- Open your aws-docker-infra/main.tf file.
- update the terraform configuration file main.tf to define a new VPC and security groups dedicated to ECS : remove the aws_instance, its related user_data block from Lab 5, the aws_security_group.allow_ssh and aws_vpc.main. As we will no longer be using a single EC2 instance directly. Add the following resources to your main.tf file. This will replace the EC2 instance setup with an ECS Fargate setup.
<img width="782" height="755" alt="1" src="https://github.com/user-attachments/assets/fc22bfee-eedc-42e6-8048-8fda8de610a2" />
<img width="1161" height="728" alt="2" src="https://github.com/user-attachments/assets/529b348d-4d57-41ba-a79a-42810050fe69" />
<img width="868" height="755" alt="3" src="https://github.com/user-attachments/assets/30ca1ddb-180c-407c-8487-659dd366075d" />
<img width="693" height="717" alt="4" src="https://github.com/user-attachments/assets/4b7dd853-7f88-4c16-b3cc-56915f701de0" />
<img width="731" height="758" alt="5" src="https://github.com/user-attachments/assets/81768816-2751-468c-9ec5-f05f8c6e7d91" />
<img width="745" height="756" alt="6" src="https://github.com/user-attachments/assets/435282cd-3acf-4879-9ea7-542fc5d84ba4" />
<img width="985" height="785" alt="7" src="https://github.com/user-attachments/assets/bc0843b9-e633-405a-bcf1-4b4b27721af0" />
<img width="708" height="809" alt="8" src="https://github.com/user-attachments/assets/2739075e-8519-406f-a655-2880b4f0338e" />
<img width="829" height="593" alt="9" src="https://github.com/user-attachments/assets/8395c543-ed90-44e3-b85f-2f16bf899600" />


### Step 2: Apply Terraform Changes for ECS and ALB
We have updated the terraform configuration files to create a VPC, subnets, security groups, ECS cluster, task definition, service, and the Application Load Balancer. Now it's time to provision all these new cloud resources using Terraform. To complete Step 2, follow the instructions below : 
- Open your command line or terminal and navigate to your aws-docker-infra folder. 
- Run terraform init again to ensure all new provider requirements are met. 
- Run terraform plan to review the extensive list of resources that will be created. This will be a much larger plan than before! 
- Run terraform apply, if the plan is correct, to provision these resources. Type yes when prompted.
<img width="988" height="803" alt="10" src="https://github.com/user-attachments/assets/20c1188d-2d46-4ff4-bde7-3f51413d6233" />
<img width="896" height="776" alt="11" src="https://github.com/user-attachments/assets/973aff6c-1aed-45bd-af64-57e91d1aa068" />
<img width="750" height="815" alt="12" src="https://github.com/user-attachments/assets/5b01dd36-430f-4302-938b-6f00ab820f8e" />
<img width="600" height="803" alt="13" src="https://github.com/user-attachments/assets/335ec86b-ff34-405a-a654-2519411a7378" />
<img width="589" height="794" alt="14" src="https://github.com/user-attachments/assets/bb858f8f-6d89-497e-8eb5-8940ca32936f" />
<img width="705" height="810" alt="15" src="https://github.com/user-attachments/assets/d0317db5-b25d-4c38-91fa-3a115de624d1" />
<img width="780" height="821" alt="16" src="https://github.com/user-attachments/assets/4f0ec8f0-a762-416a-835a-7a1b1885816b" />
<img width="788" height="806" alt="17" src="https://github.com/user-attachments/assets/64465696-a529-4d11-96d7-058dfb5195e0" />
<img width="735" height="817" alt="18" src="https://github.com/user-attachments/assets/960ad888-f4bd-4e7d-b63d-01d54f375311" />
<img width="811" height="810" alt="19" src="https://github.com/user-attachments/assets/f516935a-3036-4356-9cdd-2390951aa649" />
<img width="1153" height="814" alt="20" src="https://github.com/user-attachments/assets/0a9d183c-89f6-4517-aac6-8b66f9c4518a" />
<img width="1220" height="784" alt="21" src="https://github.com/user-attachments/assets/b3073f7c-9a3b-49dd-9e7c-cb725bc76bf2" />


- Check your infrastructure on the AWS Console. The VPC was created as well as its components.
- **VPC with subnets**
<img width="1411" height="734" alt="36" src="https://github.com/user-attachments/assets/5c414797-9266-4ea8-9294-8efc9d6bebc4" />
<img width="1154" height="438" alt="37" src="https://github.com/user-attachments/assets/7e784a36-9bf7-4e5c-a60a-503c293d0f41" />


- **NACL**
<img width="1410" height="566" alt="38" src="https://github.com/user-attachments/assets/14027352-3213-4296-a34e-ceadead0ed89" />

- **Security groups**
<img width="1420" height="663" alt="39" src="https://github.com/user-attachments/assets/16947d34-edaf-4b8a-bbaa-c35c9ebf4164" />


- **ECS cluster**
<img width="1409" height="306" alt="40" src="https://github.com/user-attachments/assets/9f3c6d1d-0750-4e21-b7e2-5b51bcef21ca" />


- **ECS Task Definition that describes our Docker container**
<img width="1453" height="372" alt="72" src="https://github.com/user-attachments/assets/89f1abcb-4b47-492b-b8e8-e27dd16c4bc8" />


- **ECS Service that runs our task**
<img width="1461" height="742" alt="77" src="https://github.com/user-attachments/assets/36616867-dd22-45f7-883b-f98570e35bce" />


- **Application Load Balancer (ALB) to route traffic**
<img width="1421" height="755" alt="52" src="https://github.com/user-attachments/assets/699a5450-ea0d-47eb-bbee-ae3f5f0e6553" />


- **ALB’s target group**
<img width="1394" height="710" alt="48" src="https://github.com/user-attachments/assets/4a2b9e26-b242-4cf7-b585-8d2005047b6f" />

- **Set up manually a Postgresql Database on AWS Console to connect it to the flask-app**:
- Set up a private subnet within the VPC, private route tables and a NAT gateway.
**Subnet** : Go to the VPC dashboard → Subnets → Create subnet  → Select our VPC → specify a name for the subnet, a CIDR → Click Create subnet.
<img width="1414" height="747" alt="41" src="https://github.com/user-attachments/assets/528e8004-ff07-4472-8731-cf344b407b7e" />

**Route table** : Go to the VPC dashboard → Route Tables → Create route table → specify a name for the RT → Select our VPC → Click Create route table.
<img width="1417" height="614" alt="42" src="https://github.com/user-attachments/assets/c7f7e749-4ea1-44c5-92d7-fc4742ddd7dd" />


**NAT Gateway** : Go to the VPC dashboard → NAT Gateways → Create NAT Gateway → specify a name for the NAT Gateway → Select our public subnet → specify connectivity type → Click Create NAT Gateway.
<img width="1400" height="660" alt="44" src="https://github.com/user-attachments/assets/1528e2e7-d63e-4ab5-be71-1dfeaa61b7fb" />


- **Update Subnet and routes tables**:
- Update Subnet : Go to the VPC dashboard → Route Tables → Select your route table → Actions → Edit subnet associations → Select the subnet to associate → Click Save associations.
- Update Routes Tables : Go to the VPC dashboard → Route Tables → Select your route table → Actions → Edit routes → Add route → Specify Destination 0.0.0.0/0, specify target Nat Gateway and nat gateway name → Click Save changes.
<img width="1425" height="559" alt="43" src="https://github.com/user-attachments/assets/88198299-7509-4cd6-ac86-f6355830f392" />
<img width="1405" height="407" alt="45" src="https://github.com/user-attachments/assets/cb8280e0-6a08-4f58-96c2-319d85a99b8e" />


- **Configure a security group for the database**:
- Go to the VPC dashboard → Security → Security groups → Create security group → specify Basic Settings (name, description, select your VPC) → Add inbound rule to allow PostgreSQL traffic on port 5432 → leave outbound rule setting default → Click Create security group.
- Create the database and associate it to the private subnet and security group within the VPC
<img width="1419" height="617" alt="46" src="https://github.com/user-attachments/assets/58a50353-ba00-425b-97fe-2de15ef582f6" />

- Go to the RDS and Aurora dashboard → Click Create Database → Define settings ( standard create, PostgreSQL, Production or Dev / Test environment, availability and durability, database name, master username, your VPC, DB subnet group, attach your security group) → Click Create database.
<img width="1408" height="757" alt="49" src="https://github.com/user-attachments/assets/972cbbb1-d859-421f-9450-02c302d1a7b7" />

- Replace the database endpoint by the actual one in the main.tf terraform file
<img width="997" height="474" alt="50" src="https://github.com/user-attachments/assets/61e82e7c-d434-4f2a-a93f-a5663e1c21b8" />

- Run terraform apply to update the configuration and allow the application to connect to the database.
<img width="1114" height="783" alt="51" src="https://github.com/user-attachments/assets/3b000dc0-b8d8-46dc-ab23-5c16dbd48ca6" />

### Step 3: Access Your Scalable Application
Your application is now running on AWS ECS Fargate, behind an Application Load Balancer. This means traffic is distributed across multiple instances of your Flask app, providing high availability. To complete Step 3, follow the instructions below : 
- Open your web browser.
- In the address bar, type the alb_dns_name you got from the terraform apply output and Press Enter.

**Error 1 : 503 Service Temporarily Unavailable**
<img width="898" height="396" alt="53" src="https://github.com/user-attachments/assets/a2d3523f-94be-46d5-aea3-7a8461d471e4" />

The Application is not reachable with the ALB DNS ! This error means that the server is not ready to handle the request. Let's troubleshoot it step-by-step.

**Troubleshooting Error 1** : 
- Step 1 : To troubleshoot this error we will first check the task status and see if they are running or not. 
Go to ECR → Clusters → my-flask-app-cluster → Tasks → Last status : all the tasks are stopped. This means that the application is not running, the containers keep crashing and draining. 
<img width="1397" height="715" alt="55" src="https://github.com/user-attachments/assets/d86f0158-b34a-458d-bc1f-0876f0223d3e" />
<img width="1445" height="649" alt="56" src="https://github.com/user-attachments/assets/6cee89ce-de21-4e18-a65d-b77a4cedcc46" />


- Step 2 : The tasks are stopped. Let's trigger a build and check the build logs. This will tell us the reason why the containers keeps crashing. 
<img width="1394" height="491" alt="60" src="https://github.com/user-attachments/assets/afb6212d-4b58-4770-ab08-12b8d9f4b39e" />

- Build Logs : this error means that the AWS CodeBuild service role, when running the build, does not have permission to read the secret from AWS Secrets Manager. 
<img width="1076" height="295" alt="61" src="https://github.com/user-attachments/assets/b84f3171-240a-4df1-ad9a-9631ef41ad97" />

- Let’s fix this error and update the terraform configuration file !
<img width="1015" height="620" alt="62" src="https://github.com/user-attachments/assets/c5ce6432-08be-4a3d-afa5-8621ea14c90a" />
<img width="1026" height="643" alt="63" src="https://github.com/user-attachments/assets/9deb45da-0163-49cc-b840-6c5d0d221f3a" />
<img width="1036" height="702" alt="64" src="https://github.com/user-attachments/assets/8abdf417-40cb-412f-a952-cccdbef133fe" />

- Step 3 : Now that we have granted the AWS code build the required permissions to have the build triggered successfully lets check the application from the web browser using the ALB DNS ! → error 2 : Database connection failed. 
<img width="1377" height="242" alt="65" src="https://github.com/user-attachments/assets/2187f688-ad2e-4ac0-a777-7c6c8c8c3ff8" />

**Error 2 : Database connection failed**
The flask application is not reachable due to database connection failure. The database "mydatabase" does not exist, which causes failure toLet’s properly configure the database before reaching the application !

**Troubleshooting Error 2** : 
- Step 1 : Check the Application logs with CloudWatch. 
- Go to CloudWatch → Log groups → ecs/my-flask-app → log events.
- Error : HTTP status code returned by Flask. 500 = Internal Server Error this means that something went wrong inside the server, causing ECS tasks to be marked unhealthy.
<img width="1428" height="685" alt="102" src="https://github.com/user-attachments/assets/7845f5f6-0d0b-441e-b369-429c504b7162" />
<img width="1410" height="465" alt="88" src="https://github.com/user-attachments/assets/97d74b75-5102-46ef-9b1c-46a8ae033ec2" />


- Step 2 : Set up a Bastion Host Server within the same VPC.
- Go to EC2 Dashboard → Instances → Launch Instance → specify the instance settings (OS, VPC → select same VPC as your RDS, subnet → select public subnet, auto assign public IP) → specify security group → allow inbound SSH traffic on port 22 and all outbound traffic. )
<img width="1394" height="353" alt="79" src="https://github.com/user-attachments/assets/0e78af40-9a44-455a-8af6-19ebc2ac8e42" />

- Step 3 : Configure Networking 
- ECS Task Security Group : allow outbound rule PostgresSQL traffic on port 5432.
<img width="1169" height="529" alt="81" src="https://github.com/user-attachments/assets/da16e38d-60e8-4eff-9a2c-1fcd55ac7494" />

- RDS Security Group : allow inbound rule PostgresSQL on port 5432 from the bastion host security group and the ECS Task security group.
<img width="1409" height="623" alt="104" src="https://github.com/user-attachments/assets/bb179b35-a53b-4a08-9dc9-9fa1b8751c64" />

- Bastion Host Server Security Group : allow outbound rule all traffic and PostgreSQL on port 5432. 
<img width="1166" height="481" alt="troubleshoot2" src="https://github.com/user-attachments/assets/3be0d64b-54f8-4c74-a386-ffbefc97148b" />

- NACL  : allow inbound and outbound rules all traffic and inbound rule PostgresSQL on port 5432 from 0.0.0.0/0
<img width="1168" height="542" alt="82" src="https://github.com/user-attachments/assets/a85b3591-a0ac-4643-b6a5-a6087a7da1d5" />


- Step 4 : Connect to the Bastion host server using SSH, install postgresql and update the package manager.
<img width="1018" height="864" alt="67" src="https://github.com/user-attachments/assets/418a113d-07e4-4add-862c-79491b37f85c" />

- Step 5 : Verify the DB connectivity 
- using the command : psql -h <rds-endpoint> -U <username> -d <database-name>
- Step 6 : create the Application Database manually to connect it to the Flask Application !
- using the commands : CREATE DATABASE mydatabase;
<img width="1210" height="457" alt="107" src="https://github.com/user-attachments/assets/3f7d7673-9d00-4a0c-bb28-847c789fa043" />


- Step 7 : Access the application 
- Now that we have troubleshoot our application let’s access it from our server using the command : curl http://alb_dns_name/ and from the web browser as well using : http://alb_dns_name
<img width="811" height="873" alt="108" src="https://github.com/user-attachments/assets/15f1fd34-b76b-4b98-a764-0244da9e2c39" />
<img width="573" height="749" alt="109" src="https://github.com/user-attachments/assets/75ffa8d6-a54d-4579-829c-e86c78da5665" />


**Expected output**: You should now see your "Simple Message Board" application running live in the cloud, served by ECS Fargate! Add some messages.
<img width="1402" height="561" alt="110" src="https://github.com/user-attachments/assets/13415fc3-1681-48fb-933b-b5f1f404aa1d" />
<img width="1277" height="702" alt="111" src="https://github.com/user-attachments/assets/045b9a95-58e6-4959-a870-87d2d1d16005" />


- Step 8 : Explore in the AWS Console the Application
- Go to the AWS Console and search for "ECS."
- Click on "Clusters" and find my-flask-app-cluster.
- Go to the "Services" tab and click on my-flask-app-service.
- You can see the "Tasks" tab to observe your running tasks (containers). You should see desired_count (2) tasks running. If one fails, ECS will automatically replace it!
<img width="1401" height="716" alt="112" src="https://github.com/user-attachments/assets/cebc29d5-b32c-4952-89d3-e21cea448194" />

- Search for "ECR" and verify your image is there.
<img width="1418" height="404" alt="113" src="https://github.com/user-attachments/assets/e499b8db-ec65-4647-8af3-3f537b6430f1" />

- Search for "EC2" -> "Load Balancers" and find my-flask-app-lb. You can see its DNS name there too.
<img width="1415" height="750" alt="114" src="https://github.com/user-attachments/assets/725ed4f1-fb93-4ef6-a66b-b3e35a851733" />

### Step 4 : Clean Up
Cleaning up the infrastructure is a critical step. It allows to destroy all AWS resources created in this lab to avoid incurring significant ongoing costs. Load balancers, ECS services, and network components can be expensive if left running.
- Go back to your local terminal.
- Navigate to your aws-docker-infra folder. Run the Terraform destroy command: terraform destroy. Type yes and press Enter to confirm.
**Expected output** : This process will take a long time as Terraform systematically tears down the ALB, ECS service, task definitions, cluster, security groups, subnets, VPC, and all associated components. Wait for "Destroy complete!" message. This ensures you don't leave resources running and incur costs.
<img width="1191" height="784" alt="115" src="https://github.com/user-attachments/assets/f2d03888-b26a-4c37-89e3-c046f62cd43d" />
<img width="951" height="835" alt="116" src="https://github.com/user-attachments/assets/af1d075a-c433-4309-be43-8b15d81e2d00" />
<img width="1037" height="841" alt="117" src="https://github.com/user-attachments/assets/1b2dbc80-a609-4a00-adda-e4bdbced0f9c" />
<img width="968" height="840" alt="118" src="https://github.com/user-attachments/assets/d5fbb9bb-2e93-436a-b625-a84579791b77" />
<img width="970" height="839" alt="119" src="https://github.com/user-attachments/assets/274e3435-62b0-468d-8c52-0ca389f2e121" />
<img width="891" height="842" alt="120" src="https://github.com/user-attachments/assets/60ef4bbc-4656-4836-b453-7d2ca0cd752a" />
<img width="864" height="840" alt="121" src="https://github.com/user-attachments/assets/ad031735-1d61-484f-817e-898133ad1234" />
<img width="878" height="834" alt="122" src="https://github.com/user-attachments/assets/547f63ba-e96e-4e27-867b-025771d9c21e" />
<img width="892" height="832" alt="123" src="https://github.com/user-attachments/assets/2123878e-4301-403a-9754-a32c41552be2" />
<img width="1283" height="812" alt="124" src="https://github.com/user-attachments/assets/f7def669-ffd3-4307-b505-aa21bb635e35" />
<img width="1442" height="794" alt="125" src="https://github.com/user-attachments/assets/042118c8-8746-4925-a7b9-d25316bcb5ba" />
<img width="825" height="834" alt="126" src="https://github.com/user-attachments/assets/e76422ed-7587-42b9-9506-14f005dfbe3e" />
<img width="881" height="837" alt="127" src="https://github.com/user-attachments/assets/3aa6cd4e-494a-41c0-bb95-61d86b609855" />
<img width="882" height="836" alt="128" src="https://github.com/user-attachments/assets/3de342e5-0331-4bea-b11d-a0931471b4ef" />
<img width="1084" height="822" alt="129" src="https://github.com/user-attachments/assets/eb3dfde2-a8b3-4442-8bdf-8bd75caac280" />
<img width="866" height="286" alt="130" src="https://github.com/user-attachments/assets/e55a0068-6ec0-4e5c-858b-08a5f69beffa" />

### Summary
This breakdown provides a step-by-step guide to Scaling Docker with AWS ECS Fargate and Load Balancing. By completing this project, we had an overview on : 
- What container orchestration is and why it's needed.
- How to use AWS ECS (Fargate launch type) to run Docker containers.
- How to define ECS Task Definitions and Services.
- How to provision an Application Load Balancer (ALB) to distribute traffic.
- How to use Terraform to deploy a highly available and scalable Docker application.

Scaling Docker with AWS ECS Fargate and Load Balancing is a powerful approach to deploy modern applications. By combining those tools, we have demonstrated how container orchestration, serverless compute, and load balancing work seamlessly together to deliver a robust, production-ready environment on AWS, ensuring fault tolerance, high availability and scalability for varying workloads without downtime.

