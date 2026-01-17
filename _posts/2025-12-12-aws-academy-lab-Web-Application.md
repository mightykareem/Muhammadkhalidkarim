# Building a Highly Available, Scalable Web Application

## Overview and Objectives

Throughout my AWS Academy courses, I have worked on various hands-on labs using different AWS services to create compute instances, install operating systems and software, deploy code, and secure resources. I gained experience with load balancing, automatic scaling, and high availability architecture.

This project challenged me to use familiar AWS services to build a complete solution independently without step-by-step guidance. It tested all the skills I had acquired throughout my learning process.

My objectives for this project were:

- Creating an architectural diagram to depict various AWS services and their interactions
- Estimating the cost using the AWS Pricing Calculator
- Deploying a functional web application on a single virtual machine backed by a relational database
- Architecting the application to separate layers (web server and database)
- Creating a virtual network configured to host a publicly accessible and secure web application
- Deploying the web application with load distributed across multiple web servers
- Configuring network security settings for the web servers and database
- Implementing high availability and scalability
- Configuring access permissions between AWS services

## Lab Environment and Budget Management

The lab environment was persistent, so any resources and data I created were retained even after each session ended. This allowed me to continue my work across multiple sessions. I had to monitor the lab budget carefully, knowing that AWS Budgets data updates every 8-12 hours, so the displayed budget might lag behind recent activity. Exceeding the budget would disable the lab account and result in loss of all progress and resources.

## AWS Service Restrictions

Access to AWS services was restricted to only those needed for this project. I encountered errors when attempting to access services or perform actions outside the scope of the project.

## Scenario

Example University's admissions department was experiencing issues with their student records web application—it was slow and often unavailable during peak admissions periods due to high inquiry volume.

I was tasked to create a proof of concept (POC) hosting the web application in the AWS Cloud. The goal was to design and implement a new architecture that would improve user experience and support thousands of concurrent users during peak periods.

My challenge was to plan, design, build, and deploy the solution following AWS Well-Architected Framework best practices. The application had to be highly available, scalable, load balanced, secure, and high performing.

## Solution Requirements

The solution needed to meet the following requirements:

**Functional:** The solution needed to meet functional requirements like viewing, adding, deleting, or modifying student records without perceivable delay.

**Load balanced:** The solution had to properly balance user traffic to avoid overloaded or underutilized resources.

**Scalable:** The solution was designed to scale to meet varying demand on the application.

**Highly available:** The solution had to have limited downtime when a web server became unavailable.

**Secure:**
- The database had to be secured and not accessible directly from public networks
- Web servers and database accessible only over appropriate ports
- The web application accessible over the internet
- Database credentials not hardcoded into the web application

**Cost optimized:** The solution was designed to keep costs low.

**High performing:** Routine operations (viewing, adding, deleting, modifying records) had to perform without perceivable delay under normal, variable, and peak loads.

## Assumptions

I planned the project under the following assumptions:

- The application was deployed in a single AWS Region (multi-Regional deployment was not required)
- HTTPS and custom domain configuration were not needed
- The solution was deployed on Ubuntu machines using provided JavaScript code
- The solution used only services within lab environment restrictions
- The database was hosted in a single Availability Zone
- The website was publicly accessible without authentication
- Cost estimation was approximate
- Security best practices like university network access and authentication were beyond the POC scope but could be implemented later

## Approach

I developed the project in phases to ensure basic functionality worked before moving to more complex architecture. This allowed me to build upon working components and test each phase before adding new complexity.


### Phase 2: Creating a Basic Functional Web Application

In this phase, I started building the solution with the objective of creating a functional web application running on a single virtual machine in a virtual network. By the end of this phase, I had a POC to demonstrate hosting the application on AWS.

#### Task 1: Creating a Virtual Network

I created a virtual network to host the web application by setting up a VPC, a public subnet, an Internet Gateway, and routing for internet access. The steps and screenshots below document the configuration.

**Step 1: Create VPC**

I started by creating a virtual private cloud (VPC) that would serve as the isolated network environment for all infrastructure. This VPC provides a dedicated network space with custom IP addressing.

- Open AWS Console → VPC → Create VPC
- Select: VPC only
- Configure:
  - Name tag: StudentApp-VPC
  - IPv4 CIDR block: 10.0.0.0/16
  - IPv6 CIDR: None
  - Tenancy: Default
- Click Create VPC

![VPC Creation: Configuration of StudentApp-VPC with 10.0.0.0/16 CIDR block](github/assets/img/posts/aws1/1.png)

**Step 2: Create Public Subnet**

Next, I created a public subnet within the VPC to host the EC2 instance. This subnet would be internet-accessible and route traffic appropriately.

- VPC → Subnets → Create subnet
- Select VPC: StudentApp-VPC
- Configure:
  - Subnet name: Public-Subnet
  - Availability Zone: us-east-1a
  - IPv4 CIDR: 10.0.1.0/24
- Click Create subnet

![Public Subnet Creation in Availability Zone us-east-1a](github/assets/img/posts/aws1/1b.png)

**Step 3: Create and Attach Internet Gateway**

To enable internet connectivity for resources in the public subnet, I created and attached an Internet Gateway to the VPC.

- VPC → Internet Gateways → Create internet gateway
- Name: StudentApp-IGW → Create
- Select the IGW → Actions → Attach to VPC → StudentApp-VPC

![Internet Gateway Creation and Attachment](github/assets/img/posts/aws1/1c.png)

**Step 4: Configure Route Table**

I configured the route table to direct internet-bound traffic through the Internet Gateway, enabling public accessibility.

- VPC → Route Tables → select the route table associated with StudentApp-VPC
- Routes → Edit routes → Add route:
  - Destination: 0.0.0.0/0 (all traffic)
  - Target: Internet Gateway (StudentApp-IGW)
- Save routes

![Route Table Configuration with Default Route to IGW](github/assets/img/posts/aws1/1d.png)

**Step 5: Associate Subnet**

Finally, I associated the public subnet with the route table to ensure all traffic from the subnet uses the configured routes.

- Go to Subnet Associations → Associate Public-Subnet

![Subnet Association with Route Table](github/assets/img/posts/aws1/1e.png)

With these steps complete, the VPC is now properly configured and internet-accessible, providing a secure, isolated network environment for the application.

#### Task 2: Creating a Virtual Machine

I created a virtual machine to host the web application using Amazon EC2 with the latest Ubuntu AMI. I installed the required web application and database using the provided JavaScript code through user data automation.

**Step 1: Launch EC2 Instance**

- Go to EC2 → Instances → Launch instance
- Name: StudentApp-EC2-POC

**Step 2: Choose AMI & Instance Type**

- AMI: Ubuntu Server (latest LTS)
- Architecture: 64-bit (x86)
- Instance type: t3.micro

![Select Ubuntu AMI and Instance Type](github/assets/img/posts/aws1/2.png)

**Step 3: Network Settings**

- VPC: StudentApp-VPC
- Subnet: Public-Subnet
- Auto-assign public IP: Enable

**Step 4: Security Group**

Create a new security group:
- Name: StudentApp-SG
- Inbound rules:
  - SSH (22) → Your IP
  - HTTP (80) → 0.0.0.0/0
- Outbound: Allow all

![Configure Network Settings and Security Group](github/assets/img/posts/aws1/2b.png)

**Step 5: User Data (IMPORTANT)**

- Scroll to Advanced details → User data
- Paste the full JavaScript setup code from the provided SolutionCodePOC script

This script automatically:
- Installs Node.js
- Installs the database
- Deploys the student records app
- Starts the application

![Add User Data Script](github/assets/img/posts/aws1/2c.png)

**Step 6: Launch Instance**

- Review configuration
- Click Launch instance
- Wait until:
  - Instance state: Running
  - Status checks: 2/2 passed

![Instance Running with Status Checks Passed](github/assets/img/posts/aws1/2d.png)


#### Task 3: Testing the Deployment

I tested the deployment by accessing the web application from the internet using the IPv4 address of the virtual machine. I performed CRUD operations (Create, Read, Update, Delete) on student records to verify functionality and ensure the application was responsive.

**Step 1: Access the Application**

- EC2 → Instances
- Copy the Public IPv4 address
- Open a browser and navigate to:
  ```
  http://<EC2-PUBLIC-IP>
  ```

![Student Records Application Running](github/assets/img/posts/aws1/3.png)

**Step 2: Functional Testing (CRITICAL)**

Perform all CRUD operations to verify the application:

- **View** student records
- **Add** a new student record
- **Modify** an existing record
- **Delete** a record

All operations should complete without noticeable delay.

![Add Student Record](github/assets/img/posts/aws1/3b.png)

![Modify and Delete Records](github/assets/img/posts/aws1/3c.png)

![Verify Records Updated](github/assets/img/posts/aws1/3d.png)


### Phase 3: Decoupling the Application Components

In this phase, I continued building by separating the database and web server infrastructure to run independently. The web application ran on a separate virtual machine, and the database ran on managed service infrastructure.

#### Task 1: Changing the VPC Configuration

I updated the virtual network components to support hosting the database separately from the application by adding private subnets in a minimum of two Availability Zones. This ensures the database is isolated from public internet access while remaining accessible to the web servers.

**Step 1: Create Private Subnet (AZ-1)**

- VPC → Subnets → Create subnet
- Configure:
  - VPC: StudentApp-VPC
  - Subnet name: Private-Subnet-A
  - Availability Zone: us-east-1b (or any AZ different from public)
  - IPv4 CIDR: 10.0.2.0/24
- Click Create subnet

![Create Private Subnet in AZ-1](github/assets/img/posts/aws1/4.png)

**Step 2: Create Private Subnet (AZ-2)**

- VPC → Subnets → Create subnet
- Configure:
  - VPC: StudentApp-VPC
  - Subnet name: Private-Subnet-B
  - Availability Zone: us-east-1c (different from AZ-1)
  - IPv4 CIDR: 10.0.3.0/24
- Click Create subnet

**Important:** Do NOT attach an Internet Gateway to these subnets—they should remain private and only accessible from your EC2 instances.

![Create Private Subnet in AZ-2](github/assets/img/posts/aws1/4b.png)

#### Task 2: Creating and Configuring the Amazon RDS Database

I created an Amazon RDS database running MySQL and configured it to allow only the web application to access the database. This ensures the database is secure, isolated in private subnets, and accessible only through the application layer.

**Step 1: Create DB Subnet Group**

- RDS → Subnet groups → Create
- Configure:
  - Name: studentapp-db-subnet-group
  - VPC: StudentApp-VPC
  - Add subnets:
    - Private-Subnet-A
    - Private-Subnet-B
- Click Create

![Create DB Subnet Group](github/assets/img/posts/aws1/5.png)

**Step 2: Create RDS MySQL Database**

- RDS → Databases → Create database
- Configure:
  - Creation method: Standard create
  - Engine: MySQL
  - Template: Dev/Test
  - DB instance identifier: studentapp-db
  - Master username: admin
  - Master password: (save securely for later use)
  - DB instance class: db.t3.micro
  - Storage: default
  - VPC: StudentApp-VPC
  - DB subnet group: studentapp-db-subnet-group
  - Public access: No
  - Availability zone: No preference
  - Additional configuration: Disable Enhanced Monitoring
- Click Create database

![Configure RDS Instance](github/assets/img/posts/aws1/6.png)

![RDS Database Created Successfully](github/assets/img/posts/aws1/6b.png)

**Step 3: Configure RDS Security Group**

- Select the RDS instance → Modify → Security group
- Configure Inbound rules:
  - Type: MySQL/Aurora (3306)
  - Source: Security group ID (StudentApp-SG)
  - This ensures only your EC2 application server can access the database

![RDS Security Group Inbound Rule](github/assets/img/posts/aws1/7.png)

#### Task 3: Configuring the Development Environment

I provisioned an AWS Cloud9 environment with a t3.micro instance to run AWS CLI commands for the remaining tasks. Cloud9 provides a cloud-based IDE with terminal access to interact with AWS services without needing to configure SSH separately.

**Step 1: Create Cloud9 Environment**

- Cloud9 → Create environment
- Configure:
  - Name: StudentApp-Cloud9
  - Environment type: EC2
  - Instance type: t3.micro
  - Platform: Amazon Linux 2
  - Network settings: Default
- Click Create

![Create Cloud9 Environment](github/assets/img/posts/aws1/8.png)

![Cloud9 Environment Details](github/assets/img/posts/aws1/8b.png)

**Step 2: Connect and Access Cloud9 IDE**

- Open the Cloud9 IDE
- Terminal opens automatically at the bottom
- You now have a command line interface to run AWS CLI commands

![Cloud9 IDE with Terminal Ready](github/assets/img/posts/aws1/9.png)


#### Task 4: Provisioning Secrets Manager

I used AWS Secrets Manager to create a secret storing the database credentials and configured the web application to use Secrets Manager. This approach ensures database credentials are not hardcoded in the application and can be rotated without redeploying code.

**Step 1: Upload Cloud9 Scripts File**

- Download the AWS Cloud9 Scripts YAML file
- In Cloud9, click File → Upload → select the cloud9-scripts.yml file
- The file will be uploaded to your Cloud9 environment

![Upload Scripts to Cloud9](github/assets/img/posts/aws1/10.png)

**Step 2: Prepare the Secret Creation Command**

The script contains placeholders that must be replaced with your actual RDS database values:

- `<username>` → admin (your RDS master username)
- `<password>` → the password you set for RDS
- `<RDS Endpoint>` → the endpoint from your RDS instance (e.g., studentapp-db.xxxxx.rds.amazonaws.com)
- `<dbname>` → STUDENTS

Example after substitution:

![Secret Creation Command with Values](github/assets/img/posts/aws1/10b.png)

**Step 3: Create the Secret**

From the Cloud9 terminal, extract and run Script-1:

```bash
sed -n '/Script-1:/,/Script-2:/p' cloud9-scripts.yml | tail -n +2 > script-1.sh
chmod +x script-1.sh
bash script-1.sh
```

This creates a secret in Secrets Manager containing:
- DB username
- DB password
- DB endpoint
- DB name

![Secret Created in Secrets Manager](github/assets/img/posts/aws1/10c.png)

Verify in: **Secrets Manager → Secrets** (the secret should be listed and accessible)

#### Task 5: Provisioning a New Instance for the Web Server

I created a new virtual machine to host the web application and installed the required application using the provided JavaScript code. I attached the LabInstanceProfile IAM profile to the EC2 instance, which included the LabRole allowing the instance to fetch database credentials securely from Secrets Manager.

**Step 1: Launch New EC2 Instance**

- EC2 → Launch instance
- Configure:
  - Name: StudentApp-Web-Server
  - AMI: Ubuntu Server (latest LTS)
  - Architecture: 64-bit (x86)
  - Instance type: t3.micro

**Step 2: Network & IAM Configuration**

- VPC: StudentApp-VPC
- Subnet: Public-Subnet
- Auto-assign public IP: Enabled
- IAM instance profile: LabInstanceProfile (this provides access to Secrets Manager)

**Step 3: Security Group**

Create or select security group with inbound rules:
- HTTP (80): 0.0.0.0/0 (allow web traffic)
- SSH (22): Your IP (allow SSH access for management)

**Step 4: User Data**

- Scroll to Advanced details → User data
- Paste the Solution Code for the App Server
- This version:
  - Does NOT install the database locally
  - Fetches database credentials from Secrets Manager
  - Connects to the RDS database

**Step 5: Launch**

- Review configuration
- Click Launch instance

![Launch Web Server EC2 Instance](github/assets/img/posts/aws1/11.png)

![Configure Network and IAM Role](github/assets/img/posts/aws1/11b.png)

![Instance Running with Public IP](github/assets/img/posts/aws1/11c.png)


#### Task 6: Migrating the Database

With the RDS database created and the new web server configured, I migrated the existing data from the original EC2 instance's local database to the managed RDS instance. This ensures data continuity and allows the application to use the centralized, highly available database.

**Step 1: Extract Migration Script**

From the Cloud9 terminal, I extracted Script-3 from the cloud9-scripts.yml file:

```bash
sed -n '/Script-3:/,/Script-4:/p' cloud9-scripts.yml | tail -n +2 > script-3.sh
chmod +x script-3.sh
```

**Step 2: Execute Migration**

I ran the migration script to transfer data from the original database to RDS:

```bash
bash script-3.sh
```

This script performs the following actions:
- Connects to the original EC2 instance database
- Extracts all student records
- Establishes a connection to the RDS MySQL database
- Inserts all data into the RDS database

**Step 3: Verify Migration**

After the migration completed, I verified that data was successfully transferred by:
- Logging into the RDS database to confirm records exist
- Checking that the application can read data from RDS without errors

With the database migrated, the application now operates with a decoupled architecture where the web server and database are independent, scalable components.


#### Task 7: Testing the Application

After decoupling the architecture, I thoroughly tested the application to ensure it functioned correctly with the new RDS database and that data had been properly migrated. This validation confirmed that the refactored architecture maintained full application functionality.

**Verification Steps:**

1. **Access the Application** - Navigate to the public IP of the new web server EC2 instance
   ```
   http://<New-EC2-Public-IP>
   ```

2. **Verify Migrated Data** - Confirm that all student records migrated from the original database are visible and accessible

3. **Functional Testing** - Perform CRUD operations:
   - **Create**: Add new student records
   - **Read**: View existing student records
   - **Update**: Modify student record details
   - **Delete**: Remove student records

4. **Performance Validation** - Ensure all operations complete without noticeable delay, indicating proper database connectivity and application performance

The decoupled architecture was now fully operational, with the web server and database running independently while maintaining complete application functionality.

### Phase 4: Implementing High Availability and Scalability

In this phase, I completed the design and fulfilled the remaining solution requirements by using the key components from earlier phases to build a scalable and highly available architecture.

#### Task 1: Creating an Application Load Balancer

To distribute incoming traffic across multiple web server instances and improve availability, I deployed an Application Load Balancer (ALB). This provides a single endpoint for users and intelligently routes traffic to healthy instances.

**Step 1: Create Target Group**

A target group defines which instances receive traffic from the load balancer and health check parameters.

- EC2 → Target Groups → Create target group
- Configure:
  - Target type: Instances
  - Name: studentapp-tg
  - Protocol: HTTP
  - Port: 80
  - VPC: StudentApp-VPC
  - Health check path: /
- Click Create target group

![Target Group Configuration](github/assets/img/posts/aws1/16.png)

**Step 2: Register Instances**

I registered the current web server instance with the target group so it receives traffic from the load balancer.

- Select the target group → Targets tab → Register targets
- Select: StudentApp-Web-Server instance
- Click Register targets

![Instance Registration with Target Group](github/assets/img/posts/aws1/16b.png)

**Step 3: Create Application Load Balancer**

I created the load balancer itself, configuring it to span multiple availability zones for high availability.

- EC2 → Load Balancers → Create load balancer
- Select: Application Load Balancer
- Configure:
  - Name: studentapp-alb
  - Scheme: Internet-facing
  - IP address type: IPv4
  - Network mapping:
    - VPC: StudentApp-VPC
    - Subnets: Public-Subnet-A and Public-Subnet-B (for multi-AZ redundancy)

![Load Balancer Configuration - Network Mapping](github/assets/img/posts/aws1/17.png)

**Step 4: Configure Security Group and Listener**

I configured security group rules to allow HTTP traffic and set up the listener to forward requests to the target group.

- Create or select security group:
  - Allow HTTP (80) from 0.0.0.0/0 (all sources)
- Listener configuration:
  - Protocol: HTTP
  - Port: 80
  - Default action: Forward to studentapp-tg
- Click Create load balancer

![Load Balancer Listener Configuration](github/assets/img/posts/aws1/17b.png)

The Application Load Balancer is now ready to distribute traffic across web server instances, providing a single, stable endpoint for users to access the application.
#### Task 2: Implementing Amazon EC2 Auto Scaling

To achieve the scalability requirement and enable the application to automatically adjust capacity based on demand, I created an AMI from the working instance and configured Auto Scaling. This allows the system to automatically launch new instances when demand increases and terminate them when demand decreases.

**Step 1: Create AMI from Working Instance**

I created an Amazon Machine Image (AMI) from the configured web server instance to use as a template for auto-scaled instances.

- EC2 → Instances → Select StudentApp-Web-Server
- Actions → Image and templates → Create image
- Configure:
  - Name: studentapp-web-ami
  - Description: Web server image with application pre-configured
- Click Create image
- Wait for AMI status to show "Available"

![Creating AMI from Running Instance](github/assets/img/posts/aws1/15.png)

![AMI Successfully Created and Available](github/assets/img/posts/aws1/15b.png)

**Step 2: Create Launch Template**

A launch template defines the configuration for instances that will be automatically launched by the Auto Scaling group.

- EC2 → Launch Templates → Create launch template
- Configure:
  - Name: studentapp-launch-template
  - AMI: studentapp-web-ami
  - Instance type: t3.micro
  - Key pair: (same as original instance)
  - IAM instance profile: LabInstanceProfile
  - Security group: StudentApp-SG
- Click Create template

**Step 3: Create Auto Scaling Group**

The Auto Scaling group manages automatic launching and termination of instances based on demand and configured policies.

- EC2 → Auto Scaling Groups → Create Auto Scaling group
- Configure:
  - Name: studentapp-asg
  - Launch template: studentapp-launch-template
  - VPC: StudentApp-VPC
  - Subnets: Public-Subnet (or both Public-Subnet-A and Public-Subnet-B for multi-AZ)
  - Group size:
    - Initial capacity: 2
    - Min size: 1
    - Max size: 4
  - Scaling policies:
    - Target tracking policy: CPU utilization 50%
    - This enables automatic scale-up when CPU exceeds 50% and scale-down when it drops below

![Auto Scaling Group Configuration and Scaling Policy](github/assets/img/posts/aws1/18.png)

![Auto Scaling Group Size and Capacity Settings](github/assets/img/posts/aws1/19.png)

- Click Create Auto Scaling Group

**Scaling Behavior:**

Once created, the Auto Scaling group will:
- Launch the specified number of instances (initial capacity: 2)
- Monitor CPU utilization across instances
- Automatically launch new instances when CPU exceeds the target threshold
- Automatically terminate instances when CPU drops and demand decreases
- Maintain at least the minimum number of instances running at all times

![Auto Scaling Group Active with Multiple Instances Running](github/assets/img/posts/aws1/20.png)

The application is now automatically scalable, responding dynamically to changes in user demand.

#### Task 3: Accessing the Application

With the Auto Scaling group and load balancer in place, I tested the application through the load balancer endpoint to ensure all instances were serving the application correctly and that the distributed architecture was functioning as intended.

**Step 1: Obtain Load Balancer DNS Name**

- EC2 → Load Balancers → Select studentapp-alb
- Copy the DNS name (e.g., studentapp-alb-xxxx.us-east-1.elb.amazonaws.com)

**Step 2: Access the Application**

Open a web browser and navigate to the load balancer DNS name:

```
http://studentapp-alb-xxxx.us-east-1.elb.amazonaws.com
```

**Step 3: Comprehensive Testing**

I performed a complete set of functional tests to verify application behavior:

1. **View Existing Records** - Confirm that migrated student data is visible and accessible
2. **Create Records** - Add new student records and verify they persist
3. **Update Records** - Modify existing student information and confirm changes
4. **Delete Records** - Remove records and verify deletion

All operations completed successfully with responsive performance, confirming that:
- The load balancer distributes traffic correctly
- All Auto Scaled instances serve the application properly
- The application accesses the RDS database correctly
- Users can interact with the application transparently, unaware of the underlying distributed infrastructure

![Application Accessed Through Load Balancer Endpoint](github/assets/img/posts/aws1/21.png)

#### Task 4: Load Testing the Application

To validate that auto scaling works as expected and the application maintains performance under increased load, I conducted load testing from the Cloud9 environment. This confirmed that the system automatically scales to meet demand.

**Step 1: Prepare Load Testing Script**

From the Cloud9 terminal, I used Apache Benchmark (ab), a standard HTTP benchmarking tool, to simulate user traffic:

```bash
ab -n 500 -c 20 http://studentapp-alb-xxxx.us-east-1.elb.amazonaws.com/
```

Where:
- `-n 500` → Total number of requests to send
- `-c 20` → Number of concurrent requests (concurrent users)

This command simulates 20 concurrent users making 500 total requests to the application through the load balancer.

**Step 2: Execute Load Test**

I ran the load test and observed the results:

```bash
# Run from Cloud9 terminal
ab -n 500 -c 20 http://<ALB-DNS-NAME>/
```

The Apache Benchmark tool provided metrics including:
- Requests per second (throughput)
- Average response time
- Minimum and maximum response times
- Failed requests (if any)

![Load Testing Results from Cloud9](github/assets/img/posts/aws1/22.png)

**Step 3: Monitor Auto Scaling**

While the load test was running, I monitored the Auto Scaling group to observe instances being launched in response to increased CPU utilization:

```bash
aws autoscaling describe-auto-scaling-instances --region us-east-1
```

This command displayed:
- Number of instances currently running
- Instance states (InService, Pending, Terminating)
- Launch times of new instances

**Expected Behavior:**

As the load test increased demand:
1. CPU utilization across instances increased
2. The Auto Scaling group detected CPU exceeded the 50% target
3. New instances were automatically launched (Pending → InService)
4. Traffic was distributed across all running instances via the load balancer
5. Response times remained stable despite increased load

**Step 4: Monitor Scale-Down**

After the load test completed and traffic decreased:

```bash
aws autoscaling describe-auto-scaling-instances --region us-east-1
```

The system automatically scaled down:
- CPU utilization dropped below the target threshold
- Excess instances were marked for termination
- The group returned to the minimum configured capacity
- Cost was optimized by running only necessary instances

![Auto Scaling Response to Load Test](github/assets/img/posts/aws1/23.png)

This load testing validated that the solution successfully met the scalability and high availability requirements, automatically adapting to varying demand patterns while maintaining application performance.