# 1. **AD Blueprint**

1. [**AD Infrastructure Diagram**](#11-AD-infrastructure-diagram)
1. [**Infrastructure deployed by AD Blueprint**](#12-infrastructure-deployed-by-vdms-vpc-blueprint)
1. [**Dependencies**](#13-dependencies)
1. [**Input variables**](#14-input-variables)
1. [**Output Variables**](#15-output-variables)
1. [**Configuration steps to be taken before deploying the environment**](#16-configuration-steps-to-be-taken-before-deploying-the-environment)

VDMS provides infrastructure for Virtual Data Center Managed Services. Which allows controlled access to admins for wide variety of private and public data sources that are not yet public domain.

## 1.1. **VDMS VPC Infrastructure Diagram**

![dot-vdms-vpc-setup - page 1](https://user-images.githubusercontent.com/20499487/32822318-f0d47fe6-c9fd-11e7-9942-b318e14cab69.jpeg)

In this deployment model, the VDMS is deployed by creating the seperate vpc with having one private subnet in each AZ of 
specified region.

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
    
1. **VPC endpoint**

    * VPC endpoint is created
    
1. **Log Group**

    * A log group is created, in which all flow log of the VDSS VPC are stored.

## 1.3. **Dependencies**

The **app-vpc-setup** Blueprint has to be deployed before deploying the **vdms-vpc-setup blueprint**. In order to specify the IAM role to aws_flow_log for monitoring the traffic coming from **app-vpc**. The VDMS VPC blueprint is configured to utilize 
one private subnet in each Availability Zones of the specified region.

## 1.4. **Input variables**

|    **Variables**               |         **Description**                                  |
|--------------------------------|--------------------------------------------------|
| Owner                          | Owner of the Product(Ex.reandeploy.service.user)         |
| Environment                    | Deploy Environment(Ex.prod/dev)                          |
| Product                        | Product Name(Ex.dot-sdc)                                 |
| region                         | CIDR block for VDMS VPC                                  |
| ami                            | Traffic type(Ex.ALL)                                     |
| instance_type                  | No of availability Zones                                 |
| subnet-id-1                    | Name prefix for subnets(Ex.vdms-vpc-private-subnet)      |
| subnet-id-2                    | 2                                                        |
| key-name                       | 4                                                        |
| host-name-1                    | 1                                                        |
| host-name-2                    | 8                                                        |
| dc1-ssm-password-parameter     | sophos vpn pool cidr range                               |
| dc2-ssm-password-parameter     | sophos vpn pool cidr range                               |
| dc1-restore-password-parameter | sophos vpn pool cidr range                               |
| dc2-restore-password-parameter | sophos vpn pool cidr range                               |
| domain-name                    | sophos vpn pool cidr range                               |
| net-bios-name                  | sophos vpn pool cidr range                               |
| site-name-1                    | sophos vpn pool cidr range                               |
| site-name-2                    | sophos vpn pool cidr range                               |
| replication-frequency          |        |

## 1.5. **Output Variables**
 The **ad-setup** is at leaf node in the hierarchy of dependency. So there is no output from this blueprint to other 
 environment.

## 1.6. **Configuration steps to be taken before deploying the environment**  

1. Add the **input** variable values to all the keys.
1. Add the parent environment name (used for vpc id to create flow log) to the depends on resource named **app-vpc-setup**
1. In the **AWS Subnet** resource named **priv_subnet**, open the count attribute and set appropriate value.
    _Example:_ Assume the no of subnet to be created is the no of availability zones and the variable 
   az_count is specified in the input json. Then count can be set as:

        "${var.az_count * length(split(",",var.priv_subnet_names))}"
   here priv_subnet_names is the variable specified in input file to name the subnets.
        
1. In the **Route table** resource named **priv_rtb**, open the count attribute and set appropriate value.

   _Example:_ Assume the no of subnet to be created is the no of availability zones and the variable 
   az_count is specified in the input json. Then count for route table can be set as:

        "count": "${var.az_count}"
   here the route table associations for each route table can be specified with following count configuration:
   
        "count": "${var.az_count * length(split(",",var.priv_subnet_names))}"
        
1. Check for the **output** variable values to all the keys.
