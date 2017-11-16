# 1. **AD Blueprint**

1. [**AD SETUP Architecture Diagram**](#11-ad-setup-architecture-diagram)
1. [**Infrastructure deployed by AD Blueprint**](#12-infrastructure-deployed-by-vdms-vpc-blueprint)
1. [**Dependencies**](#13-dependencies)
1. [**Input variables**](#14-input-variables)
1. [**Output Variables**](#15-output-variables)
1. [**Configuration steps to be taken before deploying the environment**](#16-configuration-steps-to-be-taken-before-deploying-the-environment)

AD-SETUP blueprint provides infrastructure to setup AD servers in VDMS private subnet's. It provides authentication and authorization mechanisms as well as a framework within which other related services can be deployed like AD Federated Services.
## 1.1. **AD SETUP Architecture Diagram**

![image](https://user-images.githubusercontent.com/20499487/32878497-38411820-cacc-11e7-9695-098765f317f1.png)

In this deployment model, the AD is deployed within the private subnets of VDMS VPC. 

## 1.2. **Infrastructure deployed by VDMS VPC Blueprint**

List of resources created by the Active Directory Blueprint:

1. **EC2 instances**

    * Two Domain Controllers (sdc.usdot1/sdc.usdot2) are created in different AZ in the VPC.

1. **IAM Role**

    * Two IAM roles for DCs for providing necessary permission.
    
1. **IAM Policy**

    * Two IAM Policy for DCs.
    
1. **Instance Profile**

    *  Two instance profiles for attaching role to DCs.
    
## 1.3. **Dependencies**

The **vdms-vpc-setup** Blueprint has to be deployed before deploying the **ad-setup blueprint**. In order to deploy the AD server in private subnet created by the **vdms-vpc-setup blueprint.**

## 1.4. **Input variables**

|    **Variables**               |         **Description**                                  |
|--------------------------------|--------------------------------------------------|
| Owner                          | Owner of the Product(Ex.reandeploy.service.user)         |
| Environment                    | Deploy Environment(Ex.prod/dev)                          |
| Product                        | Product Name(Ex.dot-sdc)                                 |
| region                         | CIDR block for VDMS VPC                                  |
| ami                            | AMI-ID to be setup on instance                           |
| instance_type                  | Instance type for the AD server                          |
| subnet-id-1                    | Subnet ID of VDMS private subnet-1                       |
| subnet-id-2                    | Subnet ID of VDMS private subnet-2                       |
| key-name                       | Keypair for AD                                           |
| host-name-1                    | Host Name of the instance 1                              |
| host-name-2                    | Host Name of the instance 2                              |
| dc1-ssm-password-parameter     | Domain Controller 1 Password                             |
| dc2-ssm-password-parameter     | Domain Controller 2 Password                             |
| dc1-restore-password-parameter | Restore Password for DC 1                                |
| dc2-restore-password-parameter | Restore Password for DC 2                                |
| domain-name                    | Domain name for AD(Ex.sdc.usdot)                         |
| net-bios-name                  | net bios name(Ex.SDC)                                    |
| site-name-1                    | (Ex.AZ1)                               |
| site-name-2                    | (Ex.AZ2)                               |
| replication-frequency          | Replication frequency(EX.15)       |

## 1.5. **Output Variables**
 The **ad-setup** is at leaf node in the hierarchy of environment dependency. So there is no output from this blueprint to other 
 environment.

## 1.6. **Configuration steps to be taken before deploying the environment**  

1. Add the **input** variable values to all the keys.
1. Add the parent environment name (used for vpc id to create flow log) to the depends on resource named **vdms-vpc-setup**
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
