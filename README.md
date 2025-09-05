
# Setting Up a VPC with a Public Subnet, Route Table, and Internet Gateway Using Pulumi and AWS CLI

This lab documentation demonstrates how to set up a basic AWS network infrastructure using **Pulumi (Python)** and the **AWS CLI**.  
We will create a **VPC with a public subnet**, configure an **Internet Gateway (IGW)**, and associate a **Route Table** to allow Internet access.  

By the end of this tasks, we will have a **public subnet** that can communicate with the Internet, forming the foundation for running public-facing applications on AWS.

---

## 📌 Prerequisites
- Basic knowledge of AWS networking concepts (VPCs, subnets, route tables, etc.)
- AWS credentials (Access Key ID and Secret Access Key)
- Pulumi, Python, and AWS CLI installed  
  

---
![Alt Text](https://github.com/cloudybdone/pulumi-aws-project/blob/main/Screenshot%20from%202025-09-04%2016-25-37.png)

## ⚙️ Step 1: Configure AWS CLI
Run the following command to configure AWS CLI with your credentials:

```bash
aws configure
````

Provide:

* **AWS Access Key ID**
* **AWS Secret Access Key**
* **Default region name** (e.g., `ap-southeast-1`)
* **Default output format** (`json`, `yaml`, `text`, or `table`)

---

## ⚙️ Step 2: Set Up a Pulumi Project
![Alt Text](https://github.com/cloudybdone/pulumi-aws-project/blob/main/Screenshot%20from%202025-09-04%2016-26-31.png)

### 2.1 Create a Project Directory

```bash
mkdir pulumi-project
cd pulumi-project
```

### 2.2 Initialize Pulumi Project

```bash
pulumi new aws-python
```

Follow the prompts:

* **Project name** → default or custom
* **Stack name** → e.g., `dev`
* **Description** → optional

---
![Alt Text](https://github.com/cloudybdone/pulumi-aws-project/blob/main/Screenshot%20from%202025-09-04%2016-27-09.png)


## ⚙️ Step 3: Create the Pulumi Program

Open `__main__.py` in your project directory and add the following code step by step.

### 3.1 Create a VPC

```python
import pulumi
import pulumi_aws as aws

# Create a VPC
vpc = aws.ec2.Vpc("my-vpc",
    cidr_block="10.0.0.0/16",
    tags={
        "Name": "my-vpc"
    }
)
pulumi.export("vpc_id", vpc.id)
```

---

### 3.2 Create a Public Subnet

```python
# Create a public subnet
public_subnet = aws.ec2.Subnet("public-subnet",
    vpc_id=vpc.id,
    cidr_block="10.0.1.0/24",
    availability_zone="ap-south-1a",
    map_public_ip_on_launch=True,
    tags={
        "Name": "public-subnet"
    }
)

pulumi.export("public_subnet_id", public_subnet.id)
```

---

### 3.3 Create an Internet Gateway

```python
# Create an Internet Gateway
igw = aws.ec2.InternetGateway("internet-gateway",
    vpc_id=vpc.id,
    tags={
        "Name": "igw"
    }
)

pulumi.export("igw_id", igw.id)
```

---

### 3.4 Create a Route Table and Associate with Subnet

```python
# Create a route table
public_route_table = aws.ec2.RouteTable("public-route-table",
    vpc_id=vpc.id,
    tags={
        "Name": "rt-public"
    }
)

# Create a route in the route table for the Internet Gateway
route = aws.ec2.Route("igw-route",
    route_table_id=public_route_table.id,
    destination_cidr_block="0.0.0.0/0",
    gateway_id=igw.id
)

# Associate the route table with the public subnet
route_table_association = aws.ec2.RouteTableAssociation("public-route-table-association",
    subnet_id=public_subnet.id,
    route_table_id=public_route_table.id
)

pulumi.export("public_route_table_id", public_route_table.id)
```

---

## ⚙️ Step 4: Deploy the Pulumi Stack

Run:

```bash
pulumi up
```
![Alt Text](https://github.com/cloudybdone/pulumi-aws-project/blob/main/Screenshot%20from%202025-09-04%2016-27-30.png)

* **Preview** → shows resources to be created
* **Confirm** → type `yes`

Outputs after deployment:

* `vpc_id`
* `public_subnet_id`
* `igw_id`
* `public_route_table_id`

---

## ✅ Step 5: Verify the Deployment

In the **AWS Management Console**, check:

* **VPCs** → `my-vpc`
* **Subnets** → `public-subnet` (with public IP mapping enabled)
* **Internet Gateways** → `igw` attached to `my-vpc`
* **Route Tables** → `rt-public` with route `0.0.0.0/0 → igw`
* **Associations** → subnet associated with the route table

---
![Alt Text](https://github.com/cloudybdone/pulumi-aws-project/blob/main/Screenshot%20from%202025-09-04%2016-27-55.png)

## 🧹 Step 6: Tear Down the Deployment

To destroy resources:

```bash
pulumi destroy
```
![Alt Text](https://github.com/cloudybdone/pulumi-aws-project/blob/main/Screenshot%20from%202025-09-04%2016-28-27.png)

To remove the stack completely:

```bash
pulumi stack rm dev
```

---

## 🎯 Conclusion

We have successfully set up:

* A **VPC**
* A **Public Subnet**
* An **Internet Gateway**
* A **Route Table** with Internet access

This is the foundation for deploying **public-facing applications** on AWS.

---

## 📖 References

* [Pulumi AWS Documentation](https://www.pulumi.com/docs/clouds/aws/)
* [AWS VPC Concepts](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html)



## Author

**Mohammed Salehuzzaman**\
 Sr.DevOps Engineer\
[GitHub](https://github.com/cloudybdone)
