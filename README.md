# 🚀 AWS 3 TIER ARCHITECTURE PROJECT 🌟
### In this project we are not going to use any devops tools , purely by using the services that are available in AWS.
## ARCHITECTURE 
![Project Image](PHOTOS/3-Tier-Architecture.png)
😮 `Three Tier` means 3 layers for any applications that are running in real time scenarios they will be having 3 different layers before deploying the application into the server .
<br> ☺️ I have selected 2 AZs [AZ1 & AZ2] this is bcoz we need to maintain the application for `high availability` & `fault tolerance` the reason why i have spread my resources across AZ1 & AZ2 is even if the resources that are in AZ1 gets failed I don't want the end users to face any difficulty to access my application so that is the reason why i'm also going to spread my resources across the different AZ in my VPC Network .
<br> 😏 If you want to give much higher availability to your application you can also spread your resources across multiple AZ based on the region that you are going to create.
<br> 😮 In this project i am going to use `mumbai region` throughout the project & that's the same region that i am going to use, suppose if u want to use any other regions also u can use but make sure the region whichever u have selected should have atleast 2 AZ's bcoz in our case im going to spread the resources across 2 AZs. 
### 🔴 `WEB TIER` <br> The frontend of the application i am going to keep it in the public subnet the reason why i'm going to keep is `when end users wants to access the application they should be able to accessthe application` so that's the reason why i'm going to keep webtier in public subnet.
### 🔴 `APP TIER` <br> Here i have kept App Tier in Private Subnet reason is App Tier consists of the logic that is business logic which actually means the code of the application whatever code that is required to run our application i'm going to store that code into the apptier so usually `whenever you store the code in the EC2 instances u will never generally expose those instances to the public` so that's the reason why i'm going to keep the app tier inside the private subnets .
### 🔴 `DATABSE TIER` <br> Usually database we'll kept it in the private subnet the main reason is `we don't give the outsiders to access our databases` so that's the reason why i'm going to keep the private databse that we are going to create in the private subnet.
### 🔴 `LOAD BALANCER` <br> LB will distribute the traffic among the resources that are available. <br> `Based upon the traffic i'm going to monitor the CPU Utilization Capacity & then i'll be maintaining the EC2 instances to be up & running.` <br> *As a part of this project we are going to create 2 types of load balancers* 
- 1️⃣ External Load Balancer <br> This will be attached to the web type that is the instances that are available in the public subnet.
- 2️⃣ Internal Load Balancer <br> This i'm going to attach to the instances which are available in the private subnet that is app tier

The reason why i am creating External load balancer & Internal load balancer is the in the private subnets i have my instances running across 2 AZ if by any chance one of the EC2 Instance gets failed automatically a new instance should get created so that is the reason why i'm attaching the load balancer based upon the load balancer health check performance automatically it is going to create a new instances if the health check of any of the instances is not good ,similarly the external load balancer also performs the same thing the external load balancer we are going to attach it to the internet gateway 
### `The reason why i'm attaching internet gateway to the external load balancer is bcoz the outside public wants to access the application which is available in the web tier.` 
 We are also going to create a NAT Gateway
<br> In Database Tier if you want to maintain High Availability for your application usually we will select the databases as Multi-Availability zones or Multi AZs that means our databases should spread across different AZ
<br> In this project i am going to use free tier based RDS instance so that u will not be charged anything when you are practicing also but if you want High Availability to your RDS also u can select High Availability or High AZs for your RDS in the process of creating the RDS.
<br> 🔗 Github Link https://github.com/Venkat474/3TierArchitectureApp.git

---
![Project Image](PHOTOS/project3.1.PNG)
![Project Image](PHOTOS/project3.2.PNG)

---

### 🔹 Steps for Setting Up the Project Infrastructure 🔹

1. VPC Creation
<br> Design and create a Virtual Private Cloud (VPC) to serve as the foundation for the project infrastructure.
<br> 2. S3 Bucket and IAM Role Setup
<br> Create an S3 bucket and upload the application code.
<br> Set up an IAM role with the necessary permissions and attach it to the EC2 instance.
<br> 3. Database Configuration
<br> Launch and configure an RDS instance to serve as the backend database.
<br> 4. Application Tier Setup
<br> Deploy application-tier resources, including the configuration of an internal load balancer for traffic distribution within the tier.
<br> 5. Web Tier Setup
<br> Provision web-tier resources and set up an external load balancer to manage incoming traffic from users.
<br> 6. SSL Certification and Domain Mapping
<br> Generate an SSL certificate and apply it to the external load balancer to ensure secure communication.
<br> Map the domain name to the external load balancer for public accessibility.
---
# 🔹 1️⃣ VPC Creation 🔹
### `Go to AWS`
Go to VPC [ Region = Mumbai ap-south-1 ] 
<br> ➡️ Create VPC 
<br> ➡️ VPC & more [ i am going to create all at once so i am selecting this option ] 
<br> ➡️ Name tag = demo-vpc
<br> ➡️ IPV4 CIDR Block = 192.168.0.0/22 [ i will get 1024 IPs ] 
<br> ➡️ IPV6 CIDR Block = No IPV6 CIDR Block
<br> ➡️ Tenancy = Default [ If you selected Dedicated you will get charges ]
<br> ➡️ Number of Availability Zones[AZs] = 2
<br> ➡️ Number of Public Subnets = 2
<br> ➡️ Number of Private Subnets = 4
<br> ➡️ NAT Gateways = In 1 AZs [ only 1 NAT Gateway bcoz i need NAT Gateway in 1 AZ only,So whatever resources running in the private Subnets those resources need internet connectivity bcoz we need to download & install some packages. ]
<br> ➡️ VPC endpoints = none [ Here u can also use S3 service as End points but here it is not required ] 
<br> ➡️ Create VPC
### Once VPC gets created then we need to customize the subnet that is public subnets , private subnets & so on
Now we need to edit the subnet name [ by default we are getting the subnet name ] ➡️ Click on Subnets [ left side bar ] edit it as shown 
![Project Image](PHOTOS/project3.3.PNG)
### We need to create 5 Security groups 
currently we have 2 Security groups , we are not going to use this bcoz we need to open the required ports we create our own  ➼  Click on Security groups [ left side bar ] edit it as shown
![Project Image](PHOTOS/project3.4.PNG)
![Project Image](PHOTOS/project3.5.PNG)
![Project Image](PHOTOS/project3.6.PNG)
# 🔹 2️⃣ S3 Bucket and IAM Role Setup 🔹
The reason for creating the S3 Bucket is we need to upload the code into the S3 Bucket , 
<br> Bcoz in real time uploading the complete code into the EC2 Instances is a complex task & we don't do that generally 
<br> So if u want to copy all the data to the EC2 Instances where our application should run ultimately our application should run in the EC2 instances so i have to copy the data that is available in the S3 bucket to the EC2 Instance so for that reason to avoid the complexity of uploading the code into the EC2 Instances i'm going to upload the code into the S3 bucket & then i'm going to get the code from the S3 bucket into the EC2 Instance so that is what we are going to do.
![Project Image](PHOTOS/project3.7.PNG)
- `Copy this Github repo` https://github.com/Venkat474/3TierArchitectureApp.git
- Go to any folder in your local system/create any new folder
- Go inside that folder click on `open git bash here` [ Why? Bcoz i going to download the code using git terminal ]
- git clone https://github.com/Venkat474/3TierArchitectureApp.git
- Go inside 3TierArchitectureApp we get 2 options .git & `application-code{Upload this entire folder into S3 bucket}`

#### `Go to S3`
- create bucket { Check region ➼ (Mumbai)ap-south-1 }
- General purpose
- name ➼ demo-3tier-project
- object ownership ➼ ACLs disabled
- create bucket {Now click on created bucket➼upload➼drag & drop `application-code` folder}

####  Create IAM setup
What is the reason for creating IAM Role?
<br> Bcoz , we have 2 EC2 Instances in the app Tier & these 2 instances are there in private subnet & database is also available in the private subnet so we cannot expose the resources which are there in the private subnet to the outsiders so for that what we need to do we don't want to expose so if you want to connect to the resources that is your EC2 Instances & the RDS Database which are there in the Private Subnet. We have 2 options
<br>1 . Either we have to create a `Baston host` which is the concept in VPC, we have to create a baston host in the public subnet and by using the baston host we should connect to the resources that are there in the private subnet.
<br>2 . Instead of creating the Baston host and complexing it ,In AWS we have a service which is called as `Systems manager` [ SSM ] so if we attach the appropriate role to the EC2 Instances which are there in the private subnets even though the virtual machines are there in the private subnets these EC2 Instances will not have the public IPs. So even though the virtual machines are in private subnets without any public IPs we can still connect to that VMs. how will we connect ,by using the SSM agent. 
![Project Image](PHOTOS/project3.8.PNG)
![Project Image](PHOTOS/project3.9.PNG)
**👉 The image shows that the SSM Agent inside an EC2 instance in a private subnet uses its IAM Role to securely connect to AWS Systems Manager, even without a public IP. ✅**
<br> Now the role i'm going to attach/the policy that i'm going to attach is `Amazon EC2 role for SSM`
<br> IAM Roles ➼ Roles { Left bar } ➼ create role ➼ 
<br> 1. Aws Service ➼ use case ➼ EC2 ➼ next
<br> 2. Permissions Policies ➼ AmazonEC2RoleforSSM ➼ Next
<br> 3. Name ➼ Demo-EC2-Role ➼ Create role

# 🔹 3️⃣ Database Configuration 🔹 ( We are using a RDS Service )
`Go to RDS` ➼ Left side bar click on subnet group ➼ create DB subnet group ➼ Name = DB-SNGP ➼ 
<br> vpc = demo-vpc { this is our custom vpc , not default } ➼ 
<br> AZ = ap-south-1a , ap-south-1b  {we have 2 az in diagram , also see it is in mumbai region}
<br> subnets = demo-vpc-subnet-DB1-ap-south-1a , demo-vpc-subnet-DB2-ap-south-1b = create
#### Now we need to attach this created subnet group to the database that we are going to create that is RDS
Left side bar click on Databases ➼ create database 
<br> Choose a database creation method ➼ Standard create
<br> Engine type ➼ My SQL { we are selecting this bcoz it's free } 
<br> Edition ➼ MySQL community 
<br> Engine Version ➼ MYSQL 8.0.35
<br> Templates ➼ Free tier
<br> DB instance identifier ➼ database-1
<br> Master username ➼ admin
<br> credentials management ➼ self managed = give own password , re-type password
<br> Instance Configuration ➼ db.t4g.micro
<br> Storage ➼ General Purpose SSD (gp2) = 20GIB
<br> Connectivity ➼ Don't connect to an EC2 compute resource = IPV4
<br> VPC ➼ Custom [demo-vpc]
<br> DB ➼ Subnet group ➼ db-sngp
<br> Public access ➼ No
<br> VPC security group ➼ choose existing ➼ RDS-SG {select this remove default one}
<br> AZ ➼ No preference
<br> RDS Proxy ➼ ❌ don't need
<br> Certificate authority ➼ default
<br> Database authentication ➼ password authentication
<br> Monitoring ➼ ❌
<br> Additional Configuration = name ➼ ❌ = default: mysql8.0
<br> Backup ➼ 🔲 Don't tick this
<br> AWS KMS key ➼ aws/rds (default)
<br> 🔲{don't tick this} Enable auto minor version upgrade
<br> Maintenance window ➼ No preference ➼ create database
<br> `Go to EC2` ➼ see region(Mumbai) ➼ Launch Instance ➼ Name = AppTierInstance
<br> Quick start ➼ Linux aws
<br> AMI ➼ Amazon Linux 2 AMI(HVM)-Kernels.10,SSD VolumeType[Free tier]
<br> Instance type ➼ t2.micro
<br> Key Pair ➼ proceed without a key pair
<br> Network settings ➼ edit ➼ VPC = [demo-vpc] ➼ subnet = demo-vpc-subnet-App1-ap-south-1a
<br> Auto = assign publicIP = Disable
<br> Firewall ➼ select existing security group [ App-SG ]
<br> configure storage ➼ 8GIB gp2 Root Volume
<br> Advanced details ➼ IAM instance profile = Demo-EC2-Role ➼ Launch Instance ➼ connect ➼ Session manager ➼ connect 
<br>  👉 
