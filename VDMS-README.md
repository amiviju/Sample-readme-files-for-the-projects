# 1. **VDMS VPC Blueprint**

1. [**VDMS VPC Infrastructure Diagram**](#11-vdms-vpc-infrastructure-diagram)
1. [**Infrastructure deployed by VDMS VPC Blueprint**](#12-infrastructure-deployed-by-vdms-vpc-blueprint)
1. [**Dependencies**](#13-dependencies)
1. [**Input variables**](#14-input-variables)
1. [**Output Variables**](#15-output-variables)
1. [**Configuration steps to be taken before deploying the environment**](#16-configuration-steps-to-be-taken-before-deploying-the-environment)

VDMS provides infrastructure for Virtual Data Center Managed Services which allows controlled access to admins for wide variety of private and public data sources that are not in public domain. VDMS provides secured user access to applications in commercial environments.

## 1.1. **VDMS VPC Infrastructure Diagram**

![dot-vdms-vpc-setup - page 1](https://user-images.githubusercontent.com/20499487/32822318-f0d47fe6-c9fd-11e7-9942-b318e14cab69.jpeg)

In this deployment model, the VDMS VPC is deployed having one private subnet in each AZ of specified region.

## 1.2. **Infrastructure deployed by VDMS VPC Blueprint**

List of resources created by the VDMS Blueprint:

1. **VPC**

    * VPC with the specified CIDR in the input variable is created.

1. **Subnets**

    * Two Private subnets are created in different AZ in the VPC created in previous setup.
    
1. **Route Tables**

    * Two route tables are created for each subnet and associated with the respective subnets.
    
1. **Virtual Private Gateway**

    * The VPN gateway should be attached to the VDMS VPC.
    * Route propagation is enabled in route table for the VPN gateway.
    
1. **VPC endpoint**

    * VPC endpoint is created
    
1. **Log Group**

    * A log group is created, in which all flow log of the VDSS VPC are stored.

## 1.3. **Dependencies**

The **app-vpc-setup** Blueprint has to be deployed before deploying the **vdms-vpc-setup blueprint**. The IAM role created in **app-vpc-setup** blueprint is reused in this blueprint for VPC flowlogs.

## 1.4. **Input variables**

|    **Variables**     |         **Description**                                               |
|----------------------|-----------------------------------------------------------------------|
| Owner                | Owner tag value to be associated with the created aws resources       |
| Environment          | Environment tag value to be associated with the created aws resources |
| Product              | Product tag value to be associated with the created aws resources     |
| vpc_cidr_block       | CIDR block for VDMS VPC                                               |
| flow_log_traffic_type| VPC flowlog traffic type                                              |
| az_count             | Number of availability zones to be utilized                           |
| priv_subnet_names    | Names for private subnets                                             |
| az_cidr_length       | Avalability zone CIDR length                                          |
| az_cidr_newbits      | Availability zone CIDR newbits                                        |
| subnet_cidr_length   | Subnet CIDR length                                                    |
| subnet_cidr_newbits  | Subnet CIDR newbits                                                   |
| sophos_vpn_pool_cidr | Sophos vpn pool CIDR block                                            |

## 1.5. **Output Variables**

| **Variables**           | **Description**                                                       |
|-------------------------|-----------------------------------------------------------------------|
| owner                   | Owner tag value to be associated with the created aws resources       |
| environment             | Environment tag value to be associated with the created aws resources |
| product                 | Product tag value to be associated with the created aws resources     |
| VPCId                   | VPC ID of created VDMS VPC                                            |
| Subnet1ID               | Private subnet 1 ID                                                   |
| Subnet2ID               | Private subnet 2 ID                                                   |
| Region                  | AWS region used for VDMS VPC                                          |
| VPC-CIDR                | CIDR block for VDMS VPC                           			  |	
| VPGId                   | Virtual Private Gateway ID         					  |
| VPNPoolSGId             | Security Group for VPN pool   					  |

## 1.6. **Configuration steps to be taken before deploying the environment**  

1. Add the **input** variable values to all the keys.
1. Add the parent environment name (used for vpc id to create vpc flow log) to the depends on resource named **app-vpc-setup**
1. Check for the **output** variable values to all the keys.
