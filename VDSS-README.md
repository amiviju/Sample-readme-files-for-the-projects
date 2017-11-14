# 1. **VDSS VPC Blueprint**

1. [**VDSS VPC Infrastructure Diagram**](#11-VDSS-vpc-infrastructure-diagram)
1. [**Infrastructure deployed by VDSS VPC Blueprint**](#12-infrastructure-deployed-by-VDSS-vpc-blueprint)
1. [**Dependencies**](#13-dependencies)
1. [**Input variables**](#14-input-variables)
1. [**Output Variables**](#15-output-variables)
1. [**Configuration steps to be taken before deploying the environment**](#16-configuration-steps-to-be-taken-before-deploying-the-environment)

VDMS provides infrastructure to setup ad and adfs. Which allows controlled access to admins to wide variety of private and public data sources that are not yet public
domain.

## 1.1. **VDSS VPC Infrastructure Diagram**

![dot-vdms-vpc-setup - page 1](https://user-images.githubusercontent.com/9102229/32782191-a3afc544-c96d-11e7-87cb-8c32db319c83.png)

In this deployment model, the VDMS is deployed by creating the seperate vpc with having one private subnet in each AZ of 
specified region. This infrastructure is later refered by ad-setup blueprint to setup the ad servers in each AZ.

## 1.2. **Infrastructure deployed by VDSS VPC Blueprint**

List of resources created by the VDSS Blueprint:

1. VPC

    * VPC with the specified CIDR in the input variable is created.

1. Subnets

    * Two Private subnets are created in different AZ in the VPC created in previous setup.
    * Two public subnets are created.
    
1. Route Tables

    * Four route tables are created for each subnet and associated with the respective subnets.
    
1. Internet Gateway

    * One internet gateway attached to the VPC.
    
1. VPC endpoint

    * VPC endpoint is created
    
1. Log Group

    * A log group is created, in which all flow log of the VDSS VPC are stored.

## 1.3. **Dependencies**

The dev-app-vpc-setup Blueprint has to be deployed, in order to specify the IAM role to aws_flow_log for monitering the 
traffic coming from app-vpc before deploying the vdms-vpc-setup blueprint. The VDMS blueprint is configured to utilize 
one private subnet in each Availability Zones of the specified region.

## 1.4. **Input variables**

|    **Variables**     |         **Description**                                  |
|----------------------|----------------------------------------------------------|
| Owner                | reandeploy.service.user                                  |
| Environment          | prod                                                     |
| Product              | dot-sdc                                                  |
| vpc_cidr_block       | CIDR block for VDSS VPC                                  |
| flow_log_traffic_type| Traffic type(ex.ALL)                                     |
| az_count             | No of availability Zones                                 |
| priv_subnet_names    | vdss-vpc-private-subnet                                  |
| public_subnet_names    | vdss-vpc-public-subnet                                  |
| az_cidr_length       | 2                                                        |
| az_cidr_newbits      | 4                                                        |
| subnet_cidr_length   | 1                                                        |
| subnet_cidr_newbits  | 8                                                        |

## 1.5. **Output Variables**

| **Variables**           | **Description**                                   |
|-------------------------|---------------------------------------------------|
| owner                   | reandeploy.service.user                           |
| environment             | prod                                              |
| product                 | dot-sdc                                           |
| VPCId                   | VPC ID of VDSS VPC                                |
| Subnet1ID               | ID of private subnet 1 used in VDSS               |
| Subnet2ID               | ID of private subnet 2 used in VDSS               |
| Region                  | VDSS Region                                       |
| VPC-CIDR                | CIDR block for VDSS VPC                           |
| PublicSubnet1ID         | ID of public subnet 2 used in VDSS                |
| PublicSubnet2ID         | ID of public subnet 2 used in VDSS                |
## 1.6. **Configuration steps to be taken before deploying the environment**  

1. Add the input variable values to all the keys.
1. Add the parent environment name (used for vpc id to create flow log) to the depends on resource named **app-vpc-setup**
1. In the AWS Subnet resource named priv_subnet, open the count attribute and set appropriate value.
    _Example:_ Assume the no of subnet to be created is the no of availability zones and the variable 
   az_count is specified in the input json. Then count can be set as:

        "${var.az_count * length(split(",",var.priv_subnet_names))}"
   here priv_subnet_names is the variable specified in input file to name the subnets.
        
1. In the Route table resource named priv_rtb, open the count attribute and set appropriate value.

   _Example:_ Assume the no of subnet to be created is the no of availability zones and the variable 
   az_count is specified in the input json. Then count for route table can be set as:

        "count": "${var.az_count}"
   here the route table associations for each route table can be specified with following count configuration:
   
        "count": "${var.az_count * length(split(",",var.priv_subnet_names))}"
