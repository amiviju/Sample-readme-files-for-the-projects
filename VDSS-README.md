# 1. **VDSS VPC Blueprint**

1. [**VDSS VPC Infrastructure Diagram**](#11-VDSS-vpc-infrastructure-diagram)
1. [**Infrastructure deployed by VDSS VPC Blueprint**](#12-infrastructure-deployed-by-VDSS-vpc-blueprint)
1. [**Dependencies**](#13-dependencies)
1. [**Input variables**](#14-input-variables)
1. [**Output Variables**](#15-output-variables)
1. [**Configuration steps to be taken before deploying the environment**](#16-configuration-steps-to-be-taken-before-deploying-the-environment)

VDSS VPC provides infrastructure for Virtual Data Center Security Services which allows controlled access to admins to wide variety of private and public data sources that are not in public domain. VDSS provides network security to protect applications & data in commercial environments.

## 1.1. **VDSS VPC Infrastructure Diagram**

![dot-vdss-vpc-setup - page 1](https://user-images.githubusercontent.com/20499487/32821072-8da252dc-c9f7-11e7-84f7-6a6991b49fcf.jpeg)

In this deployment model, the VDSS VPC is having one private subnet and one public subnet in each AZ of specified region. The public subnet will be used to host Sophos which will be the single point of entry for users into the environment.

## 1.2. **Infrastructure deployed by VDSS VPC Blueprint**

List of resources created by the VDSS Blueprint:

1. **VPC**

    * VPC with the specified CIDR in the input variable is created.

1. **Subnets**

    * Two Private subnets are created in different AZ in the VPC created in previous setup.
    * Two public subnets are created.
    
1. **Route Tables**

    * Four route tables are created for each subnet and associated with the respective subnets.
    
1. **Internet Gateway**

    * One internet gateway attached to the VPC.
    
1. **VPC endpoint**

    * VPC endpoint is created
    
1. **Log Group**

    * A log group is created, in which all flow log of the VDSS VPC are stored.

## 1.3. **Dependencies**

The **app-vpc-setup** blueprint has to be deployed before deploying the **vdss-vpc-setup** blueprint. The IAM role created in **app-vpc-setup** blueprint is reused in this blueprint for VPC flowlogs.

## 1.4. **Input variables**

|    **Variables**     |         **Description**                                               |
|----------------------|-----------------------------------------------------------------------|
| Owner                | Owner tag value to be associated with the created aws resources       |
| Environment          | Environment tag value to be associated with the created aws resources |
| Product              | Product tag value to be associated with the created aws resources     |
| vpc_cidr_block       | CIDR block for VDSS VPC                                               |
| flow_log_traffic_type| VPC flowlog traffic type                                              |
| az_count             | Number of availability zones to be utilized                           |
| priv_subnet_names    | Names for private subnets                                             |
| public_subnet_names  | Names for public subnets                                              |
| az_cidr_length       | Avalability zone CIDR length                                          |
| az_cidr_newbits      | Availability zone CIDR newbits                                        |
| subnet_cidr_length   | Subnet CIDR length                                                    |
| subnet_cidr_newbits  | Subnet CIDR newbits                                                   |

## 1.5. **Output Variables**

| **Variables**           | **Description**                                                       |
|-------------------------|-----------------------------------------------------------------------|
| owner                   | Owner tag value to be associated with the created aws resources       |
| environment             | Environment tag value to be associated with the created aws resources |
| product                 | Product tag value to be associated with the created aws resources     |
| VPCId                   | VPC ID of created VDSS VPC                                            |
| Subnet1ID               | Private subnet 1 ID                                                   |
| Subnet2ID               | Private subnet 2 ID                                                   |
| Region                  | AWS region used for VDSS VPC                                          |
| VPC-CIDR                | CIDR block for VDSS VPC                                               |
| PublicSubnet1ID         | Public subnet 2 ID                					  |
| PublicSubnet2ID         | Public subnet 2 ID                					  |

## 1.6. **Configuration steps to be taken before deploying the environment**  

1. Add the **input variable** values to all the keys.
1. Add the parent environment name (used for vpc id to create flow log) to the depends on resource named **app-vpc-setup**
1. Check for the ***output variable** values to all the keys.
