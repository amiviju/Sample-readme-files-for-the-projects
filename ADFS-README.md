# 1. **ADFS-Setup Blueprint**

1. [**ADFS-Setup Architecture Diagram**](#11-adfs-setup-architecture-diagram)
1. [**Infrastructure deployed by ADFS-Setup Blueprint**](#12-infrastructure-deployed-by-adfs-setup-blueprint)
1. [**Dependencies**](#13-dependencies)
1. [**Input variables**](#14-input-variables)
1. [**Output Variables**](#15-output-variables)
1. [**Configuration steps to be taken before deploying the environment**](#16-configuration-steps-to-be-taken-before-deploying-the-environment)

AD-SETUP blueprint provides infrastructure to setup AD servers in VDMS private subnet's. It provides authentication and authorization mechanisms as well as a framework within which other related services can be deployed like AD Federated Services.
## 1.1. **ADFS-Setup Architecture Diagram**

![image](https://user-images.githubusercontent.com/20499487/32883733-2ad223de-cade-11e7-81e3-af67dac7ba78.png)

In this deployment model, the AD is deployed within the private subnets of VDMS VPC. 

## 1.2. **Infrastructure deployed by ADFS-Setup Blueprint**

List of resources created by the ADFS Blueprint:

1. **EC2 instances**

    * ADFS server is created in private subnet of  VDMS VPC.

1. **IAM Role**

    *  Two IAM roles for ADFS for providing necessary permission.
    
1. **IAM Policy**

    *  Two IAM Policy for ADFS.
    
1. **Instance Profile**

    *  Two instance profiles for attaching role to ADFS.
    
## 1.3. **Dependencies**

The **vdms-vpc-setup** Blueprint has to be deployed before deploying the **adfs-setup blueprint**. In order to deploy the AD server in private subnet created by the **vdms-vpc-setup blueprint.**

## 1.4. **Input variables**

|    **Variables**               |         **Description**                                  |
|--------------------------------|----------------------------------------------------------|
| Owner                          | Owner of the Product(Ex.reandeploy.service.user)         |
| Environment                    | Deploy Environment(Ex.prod/dev)                          |
| Product                        | Product Name(Ex.dot-sdc)                                 |
| region                         | CIDR block for VDMS VPC                                  |
| ami                            | AMI-ID to be setup on instance                           |
| instance_type                  | Instance type for the AD server                          |
| subnet-id                      | Subnet ID of VDMS private subnet-1                       |
| sg-id                          | Subnet ID of VDMS private subnet-2                       |
| key-name                       | Keypair for AD                                           |
| host-name                      | Host Name of the instance 1                              |
| domain-name                    | Host Name of the instance 2                              |
| net-bios-name                  | Domain Controller 1 Password                             |
| adfs-service-account           | Domain name for AD(Ex.sdc.usdot)                         |
| dc1-private-ip                 | net bios name(Ex.SDC)                                    |
| dc2-private-ip                 | (Ex.AZ1)                               |
| ssl-bucket                     | (Ex.AZ2)                               |
| rules-bucket                   | Replication frequency(EX.15)       |
| ssl-prefix                     |                 |
| ssl-key                        |     |
| issuance-auth-prefix           |     |
| issuance-auth-key              |     |
| claim-rules-prefix             |          |
| claim-rules-key                |       |
| self-cert-flag                 |     |
| saml-provider-name             |     |
| federation-service-display     |     |
| federation-service-name        |     |
| dc1-ssm-password-parameter     |     |
| adfs-ssm-password-parameter    |     |
| ssl-cert-password-parameter    |     |  

## 1.5. **Output Variables**
 The **adfs-setup** is at leaf node in the hierarchy of environment dependency. So there is no output from this to other environment.

## 1.6. **Configuration steps to be taken before deploying the environment**  

1. Add the **input** variable values to all the keys.

         Four SSM Password Parameters in Parameter Store :
                 a. Domain Controller 1 Password
                 b. Domain Controller 2 Password
                 c. Restore Password for DC 1
                 d. Restore Password for DC2

1. Add the parent environment name (used for vpc id to create flow log) to the depends on resource named **vdms-vpc-setup**
1. AWS security froup resource named **AD-SG*** should be configured with VDMS VPC such that all-traffic Inbound rule for itself (Self-point SG rule).        
