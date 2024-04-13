# [TOC]

[SAS Viya on AWS ROSA - ROSA prerequisites](https://github.com/redhat-gpst/sas-viya-rosa/blob/main/rosa/README.md#sas-viya-on-aws-rosa---rosa-prerequisites)
- [Enable ROSA](https://github.com/redhat-gpst/sas-viya-rosa/blob/main/rosa/README.md#enable-rosa)
- [Creating a Virtual Private Cloud (VPC) using Terraform](https://github.com/redhat-gpst/sas-viya-rosa/blob/main/rosa/README.md#creating-a-virtual-private-cloud-vpc-using-terraform)
- [Creating Account-wide roles](https://github.com/redhat-gpst/sas-viya-rosa/blob/main/rosa/README.md#creating-account-wide-roles)
- [Creating OIDC configuration](https://github.com/redhat-gpst/sas-viya-rosa/blob/main/rosa/README.md#creating-oidc-configuration)
- [Creating Operator roles](https://github.com/redhat-gpst/sas-viya-rosa/blob/main/rosa/README.md#creating-operator-roles)

[SAS Viya on AWS ROSA - ROSA deployment notes](https://github.com/redhat-gpst/sas-viya-rosa/blob/main/rosa/README.md#sas-viya-on-aws-rosa---rosa-deployment-notes)
- [Create a ROSA cluster using the CLI](https://github.com/redhat-gpst/sas-viya-rosa/blob/main/rosa/README.md#create-a-rosa-cluster-using-the-cli)
- [Create a ROSA cluster using the web ui on the Hybrid Cloud Console](https://github.com/redhat-gpst/sas-viya-rosa/blob/main/rosa/README.md#create-a-rosa-cluster-using-the-web-ui-on-the-hybrid-cloud-console)
  - [STEP 1: Control Plane](https://github.com/redhat-gpst/sas-viya-rosa/blob/main/rosa/README.md#step-1-control-plane)
  - [STEP 2: Accounts and Roles](https://github.com/redhat-gpst/sas-viya-rosa/blob/main/rosa/README.md#step-2-accounts-and-roles)
  - [STEP 3: Cluster Settings](https://github.com/redhat-gpst/sas-viya-rosa/blob/main/rosa/README.md#step-3-cluster-settings)
  - [STEP 4: Networking](https://github.com/redhat-gpst/sas-viya-rosa/blob/main/rosa/README.md#step-4-networking)
  - [STEP 5: Cluster Roles and Policies](https://github.com/redhat-gpst/sas-viya-rosa/blob/main/rosa/README.md#step-5-cluster-roles-and-policies)
  - [STEP 6: Cluster Updates](https://github.com/redhat-gpst/sas-viya-rosa/blob/main/rosa/README.md#step-6-cluster-updates)
  - [STEP 7: Review and Create](https://github.com/redhat-gpst/sas-viya-rosa/blob/main/rosa/README.md#step-7-review-and-create)
  - [Cluster Creation Progress](https://github.com/redhat-gpst/sas-viya-rosa/blob/main/rosa/README.md#cluster-creation-progress)
  - [Cluster Creation Completion](https://github.com/redhat-gpst/sas-viya-rosa/blob/main/rosa/README.md#cluster-creation-completion)

[ROSA Post-install configuration](https://github.com/redhat-gpst/sas-viya-rosa/blob/main/rosa/README.md#rosa-post-install-configuration)
- [Create four additional machine pools for use with SAS Viya](https://github.com/redhat-gpst/sas-viya-rosa/blob/main/rosa/README.md#create-four-additional-machine-pools-for-use-with-sas-viya)
- [Setting up the AWS EFS CSI Driver Operator](https://github.com/redhat-gpst/sas-viya-rosa/blob/main/rosa/README.md#setting-up-the-aws-efs-csi-driver-operator)
- [Configure AWS RDS service](https://github.com/redhat-gpst/sas-viya-rosa/blob/main/rosa/README.md#configure-aws-rds-service)
- [Configure ROSA to pull images from AWS ECR](https://github.com/redhat-gpst/sas-viya-rosa/blob/main/rosa/README.md#configure-rosa-to-pull-images-from-aws-ecr)
- [Install cert-utils-operator](https://github.com/redhat-gpst/sas-viya-rosa/blob/main/rosa/README.md#install-cert-utils-operator)

# SAS Viya on AWS ROSA - ROSA prerequisites

## Enable ROSA

To create a ROSA cluster, you must first enable the ROSA service in the AWS ROSA console. The AWS ROSA console verifies if your AWS account has the necessary AWS Marketplace permissions, service quotas, and the Elastic Load Balancing (ELB) service-linked role named AWSServiceRoleForElasticLoadBalancing. 
If any of these prerequisites are missing, the console provides guidance on how to configure your account to meet the prerequisites.

1. Navigate to the [ROSA console](https://console.aws.amazon.com/rosa).
2. Choose Get started.
3. On the Verify ROSA prerequisites page, select I agree to share my contact information with Red Hat.
4. Choose Enable ROSA .
5. Once the page has verified your service quotas meet ROSA prerequisites and the ELB service-linked role is created, open a new terminal session to create your first ROSA cluster using the ROSA CLI.

Sample output
![image](https://github.com/redhat-gpst/sas-viya-rosa/assets/48925593/6fc41552-301d-41d2-84a3-4f8b9efaba31)

![image](https://github.com/redhat-gpst/sas-viya-rosa/assets/48925593/e6df2977-0661-4749-8ad5-3617e2b993ed)



## Creating a Virtual Private Cloud (VPC) using Terraform

Terraform is a tool that allows you to create various resources using an established template. The following process uses the default options as required to create a ROSA with HCP cluster. 
For more information about using Terraform, see the HashiCorp Terraform documentation. See the Terraform VPC repository for a detailed list of all options available when customizing the VPC for your needs.
**Prerequisites**
- You have installed Terraform version 1.4.0 or newer on your machine.
- You have installed Git on your machine.

Perform the following steps to Create a VPC by using a Terraform template Creating a Virtual Private Cloud for your ROSA with HCP clusters:

1. Open a shell prompt and clone the Terraform VPC repository by running the following command:
```shell
git clone https://github.com/openshift-cs/terraform-vpc-example
```

2. Navigate to the created directory by running the following command:
```shell
cd terraform-vpc-example
```

3. Initiate the Terraform file by running the following command:
```shell
terraform init
```

A message confirming the initialization appears when this process completes.

4. To build your VPC Terraform plan based on the existing Terraform template, run the plan command. You must include your AWS region. You can choose to specify a cluster name. A `rosa.tfplan` file is added to the `hypershift-tf` directory after the terraform plan completes. 

For more detailed options, see the Terraform VPC repository’s README file.

```shell
export REGION=us-east-2
export ROSA_CLUSTER_NAME=sasviya

terraform plan -out rosa.tfplan \
      -var region=$REGION \
      -var cluster_name=$ROSA_CLUSTER_NAME
```

Capture the values of the Terraform-provisioned private, public, and machine pool subnet IDs as environment variables to use when creating your ROSA with HCP cluster by running the following commands:

```shell
export SUBNET_IDS=$(terraform output -raw cluster-subnets-string)
```
You can verify that the variables were correctly set with the following command:
```shell
$ echo $SUBNET_IDS
```
Sample output
```shell
$ subnet-0a6a57e0f784171aa,subnet-078e84e5b10ecf5b0
```



## Creating Account-wide roles
Before creating a ROSA with HCP cluster, you must create the necessary IAM roles and policies and the OpenID Connect (OIDC) configuration. 

1. Create the required IAM account roles and policies.
```shell
rosa create account-roles --hosted-cp --mode auto
```
Sample output
```shell
I: Logged in as 'pfarley@redhat.com' on 'https://api.openshift.com'
I: Validating AWS credentials...
I: AWS credentials are valid!
I: Validating AWS quota...
I: AWS quota ok. If cluster installation fails, validate actual AWS resource usage against https://docs.openshift.com/rosa/rosa_getting_started/rosa-required-aws-service-quotas.html
I: Verifying whether OpenShift command-line tool is available...
I: Current OpenShift Client Version: 4.14.12
I: Creating account roles
I: Creating hosted CP account roles using 'arn:aws:iam::XXXXXXXXXXXX:user/open-environment-l6sw8-admin'
I: Created role 'ManagedOpenShift-HCP-ROSA-Installer-Role' with ARN 'arn:aws:iam::XXXXXXXXXXXX:role/ManagedOpenShift-HCP-ROSA-Installer-Role'
I: Created role 'ManagedOpenShift-HCP-ROSA-Support-Role' with ARN 'arn:aws:iam::XXXXXXXXXXXX:role/ManagedOpenShift-HCP-ROSA-Support-Role'
I: Created role 'ManagedOpenShift-HCP-ROSA-Worker-Role' with ARN 'arn:aws:iam::XXXXXXXXXXXX:role/ManagedOpenShift-HCP-ROSA-Worker-Role'
``` 


## Creating OIDC configuration

For more information about IAM roles and policies for ROSA with HCP, see [AWS managed IAM policies for ROSA](https://docs.aws.amazon.com/rosa/latest/userguide/security-iam-awsmanpol.html).

This procedure uses the auto mode of the ROSA CLI to automatically create the OIDC configuration necessary to create a ROSA with HCP cluster.

1. Create the OpenID Connect (OIDC) configuration that enables user authentication to the cluster. This configuration is registered to be used with OpenShift Cluster Manager (OCM).
```shell
rosa create oidc-config --mode=auto
```
Sample output
```shell
? Would you like to create a Managed (Red Hat hosted) OIDC Configuration Yes
I: Setting up managed OIDC configuration
I: To create Operator Roles for this OIDC Configuration, run the following command and remember to replace <user-defined> with a prefix of your choice:
	rosa create operator-roles --prefix <user-defined> --oidc-config-id 29hejou9vg7ltren6mdsnpt2k015eqp3
If you are going to create a Hosted Control Plane cluster please include '--hosted-cp'
I: Creating OIDC provider using 'arn:aws:iam::XXXXXXXXXXXX:user/open-environment-l6sw8-admin'
? Create the OIDC provider? Yes
I: Created OIDC provider with ARN 'arn:aws:iam::XXXXXXXXXXXX:oidc-provider/oidc.op1.openshiftapps.com/29hejou9vg7ltren6mdsnpt2k015eqp3'

2. Save the OIDC configuration ID as a variable to use later. Run the following command to save the variable:
```shell
export OIDC_ID=29hejou9vg7ltren6mdsnpt2k015eqp3
```

## Creating Operator roles

1. Create the required IAM operator roles, replacing <OIDC_CONFIG_ID> with the OIDC config ID copied previously.
2. 
**Important**
You must supply a prefix in <PREFIX_NAME> when creating the Operator roles. Failing to do so produces an error.
```shell
rosa create operator-roles --prefix <PREFIX_NAME> \
      --oidc-config-id <OIDC_CONFIG_ID> --hosted-cp

rosa create operator-roles --prefix ManagedOpenShift \
      --oidc-config-id $OIDC_ID --hosted-cp

rosa create operator-roles --prefix ManagedOpenShift \
      --oidc-config-id 29hejou9vg7ltren6mdsnpt2k015eqp3 --hosted-cp
```





# SAS Viya on AWS ROSA - ROSA deployment notes

[TOC]



## Create a ROSA cluster using the CLI

Create your ROSA with HCP cluster with a single, initial machine pool, publicly available API, and publicly available Ingress by running the following command: 

```shell
export REGION=us-east-2
export ROSA_VERSION=4.14.11
export ROSA_CLUSTER_NAME=sasviya
rosa create cluster --cluster-name=$ROSA_CLUSTER_NAME \
      --sts --mode=auto --hosted-cp --region $REGION \
      --version $ROSA_VERSION\
      --operator-roles-prefix <operator-role-prefix> \
      --oidc-config-id $OIDC_ID \
      --subnet-ids=$SUBNET_IDS
```

Sample output
```shell
rosa create cluster --cluster-name=rosa-sasviya --sts --mode=auto \
     --hosted-cp --operator-roles-prefix ManagedOpenShift \
     --oidc-config-id 29gl5vkdp33gl7qjjbrm8el2vc1oid62 \
     --subnet-ids=subnet-0dfeff8ef81ead03b,subnet-074424179474a07d9

rm8el2vc1oid62 --subnet-ids=subnet-0dfeff8ef81ead03b,subnet-074424179474a07d9
I: Using '771361553200' as billing account
I: Using 'XXXXXXXXXXXX' as billing account
I: To use a different billing account, add --billing-account xxxxxxxxxx to previous command
I: Using arn:aws:iam::XXXXXXXXXXXX:role/ManagedOpenShift-HCP-ROSA-Installer-Role for the Installer role
I: Using arn:aws:iam::XXXXXXXXXXXX:role/ManagedOpenShift-HCP-ROSA-Worker-Role for the Worker role
I: Using arn:aws:iam::XXXXXXXXXXXX:role/ManagedOpenShift-HCP-ROSA-Support-Role for the Support role
I: Reusable OIDC Configuration detected. Validating trusted relationships to operator roles: 
I: Creating cluster 'rosa-sasviya'
I: To view a list of clusters and their status, run 'rosa list clusters'
I: Cluster 'rosa-sasviya' has been created.
I: Once the cluster is installed you will need to add an Identity Provider before you can login into the cluster. See 'rosa create idp --help' for more information.

Name:                       rosa-sasviya
ID:                         29glkgp83eut7jvmcp0ntfvqhum5p07h
External ID:                477a8eba-69e7-450a-be9e-54c4f56b40c6
Control Plane:              ROSA Service Hosted
OpenShift Version:          4.14.11
Channel Group:              stable
DNS:                        Not ready
AWS Account:                XXXXXXXXXXXX
AWS Billing Account:        XXXXXXXXXXXX
API URL:                    
Console URL:                
Region:                     us-east-2
Availability:
 - Control Plane:           MultiAZ
 - Data Plane:              SingleAZ
Nodes:
 - Compute (desired):       2
 - Compute (current):       0
Network:
 - Type:                    OVNKubernetes
 - Service CIDR:            172.30.0.0/16
 - Machine CIDR:            10.0.0.0/16
 - Pod CIDR:                10.128.0.0/14
 - Host Prefix:             /23
 - Subnets:                 subnet-0dfeff8ef81ead03b, subnet-074424179474a07d9
EC2 Metadata Http Tokens:   optional
STS Role ARN:               arn:aws:iam::XXXXXXXXXXXX:role/ManagedOpenShift-HCP-ROSA-Installer-Role
Support Role ARN:           arn:aws:iam::XXXXXXXXXXXX:role/ManagedOpenShift-HCP-ROSA-Support-Role
Instance IAM Roles:
 - Worker:                  arn:aws:iam::XXXXXXXXXXXX:role/ManagedOpenShift-HCP-ROSA-Worker-Role
Operator IAM Roles:
 - arn:aws:iam::XXXXXXXXXXXX:role/ManagedOpenShift-openshift-ingress-operator-cloud-credentials
 - arn:aws:iam::XXXXXXXXXXXX:role/ManagedOpenShift-openshift-cluster-csi-drivers-ebs-cloud-credent
 - arn:aws:iam::XXXXXXXXXXXX:role/ManagedOpenShift-openshift-cloud-network-config-controller-cloud
 - arn:aws:iam::XXXXXXXXXXXX:role/ManagedOpenShift-kube-system-control-plane-operator
 - arn:aws:iam::XXXXXXXXXXXX:role/ManagedOpenShift-kube-system-kms-provider
 - arn:aws:iam::XXXXXXXXXXXX:role/ManagedOpenShift-kube-system-kube-controller-manager
 - arn:aws:iam::XXXXXXXXXXXX:role/ManagedOpenShift-kube-system-capa-controller-manager
 - arn:aws:iam::XXXXXXXXXXXX:role/ManagedOpenShift-openshift-image-registry-installer-cloud-creden
Managed Policies:           Yes
State:                      waiting (Waiting for user action)
Private:                    No
Created:                    Feb 19 2024 19:13:11 UTC
User Workload Monitoring:   Enabled
Details Page:               https://console.redhat.com/openshift/details/s/2cbAdM4nAuPt7pKtwXDcv60gMVt
OIDC Endpoint URL:          https://oidc.op1.openshiftapps.com/29gl5vkdp33gl7qjjbrm8el2vc1oid62 (Managed)
Audit Log Forwarding:       Disabled

I: Preparing to create operator roles.
I: Reusable OIDC Configuration detected. Validating trusted relationships to operator roles: 
I: Operator Roles already exists
I: Preparing to create OIDC Provider.
I: OIDC provider already exists
I: To determine when your cluster is Ready, run 'rosa describe cluster -c rosa-sasviya'.
I: To watch your cluster installation logs, run 'rosa logs install -c rosa-sasviya --watch'.
```

## Create a ROSA cluster using the web ui on the Hybrid Cloud Console. 

See [Hybrid Cloud Console](https://console.redhat.com/openshift/create/rosa/getstarted) for more information about creating a ROSA cluster using the web ui.

[Getting started with Red Hat OpenShift Service on AWS (ROSA)](https://cloud.redhat.com/learn/getting-started-red-hat-openshift-service-aws-rosa)
Learning path • 15 resources • 2 hrs and 49 mins • Published on May 22, 2023

### STEP 1: Control Plane

![image](https://github.com/redhat-gpst/sas-viya-rosa/assets/48925593/27b7ba4b-31b7-4b54-955e-a2367387bd6b)


### STEP 2: Accounts and Roles

![image](https://github.com/redhat-gpst/sas-viya-rosa/assets/48925593/650bdfbc-c3b1-4da6-8c97-bc43c258bed5)


### STEP 3: Cluster Settings

![image](https://github.com/redhat-gpst/sas-viya-rosa/assets/48925593/fb6a3db7-fa86-4b90-bc3d-052724d8cf64)


### STEP 4: Networking

![image](https://github.com/redhat-gpst/sas-viya-rosa/assets/48925593/4718d1a5-ab41-42c1-b240-42a9fa868f65)

![image](https://github.com/redhat-gpst/sas-viya-rosa/assets/48925593/cdd8aa0b-78c9-47e6-a58b-54e6b7adab22)


### STEP 5: Cluster Roles and Policies

![image](https://github.com/redhat-gpst/sas-viya-rosa/assets/48925593/7da3c1a4-572e-43bf-8ed9-9bb6ddd84d90)

```shell
rosa create operator-roles --prefix "sasviya-z5sz" --oidc-config-id "29gl5vkdp33gl7qjjbrm8el2vc1oid62" --hosted-cp --installer-role-arn arn:aws:iam::XXXXXXXXXXXX:role/ManagedOpenShift-HCP-ROSA-Installer-Role
```

### STEP 6: Cluster Updates

![image](https://github.com/redhat-gpst/sas-viya-rosa/assets/48925593/dfbebe48-c8e8-4d64-9c6e-155a6a42d478)


### STEP 7: Review and Create

![image](https://github.com/redhat-gpst/sas-viya-rosa/assets/48925593/6acd55b1-4ee3-4abd-ab36-1b76bdde4c93)
![image](https://github.com/redhat-gpst/sas-viya-rosa/assets/48925593/b63dfe56-ff80-474b-991d-0a126256d0e0)
![image](https://github.com/redhat-gpst/sas-viya-rosa/assets/48925593/8e953163-847f-4b91-af33-4ed0a116f96c)


### Cluster Creation Progress

![image](https://github.com/redhat-gpst/sas-viya-rosa/assets/48925593/62144371-f591-4de9-8b1d-96870c80c2a0)

![image](https://github.com/redhat-gpst/sas-viya-rosa/assets/48925593/4dffeb39-d16e-41a9-9ea1-3b99c2abf094)

![image](https://github.com/redhat-gpst/sas-viya-rosa/assets/48925593/20eabcf8-41d3-4e9e-b579-f31a2aa699b8)

### Cluster Creation Completion

![image](https://github.com/redhat-gpst/sas-viya-rosa/assets/48925593/8e00093c-8306-4017-983b-a16d3fba2a66)
![image](https://github.com/redhat-gpst/sas-viya-rosa/assets/48925593/72c68772-6b5a-41bb-8b6d-dfcb193cae48)


# ROSA Post-install configuration

## Create four additional machine pools for use with SAS Viya.
Sizing recommendations for OpenShift; based on the minimum resource requirements for a small deployment of SAS Viya. 
[Creating machine pools](https://docs.openshift.com/rosa/rosa_cluster_admin/rosa_nodes/rosa-managing-worker-nodes.html#creating_a_machine_pool_rosa-managing-worker-nodes)

1. Create a CAS machine pool called `cas-pool` that uses the `r5.2xlarge` instance type and has 3 compute (also known as worker) node replicas, adding the workload-specific labels and taints:

```shell
export ROSA_CLUSTER_NAME=sasviya
rosa create machinepool --cluster=$ROSA_CLUSTER_NAME \
      --name=cas-pool --replicas=3 \
      --instance-type=r5.2xlarge \
      --labels=workload.sas.com/class=cas \
      --taints=workload.sas.com/class=cas:NoSchedule
```

2. Create a Compute machine pool called `compute-pool` that uses the `r5.xlarge` instance type with autoscaling enabled. The minimum compute node limit is 3 and the maximum is 6 overall. This example also adds the workload-specific labels and taints:

```shell
rosa create machinepool --cluster=$ROSA_CLUSTER_NAME \
      --name=compute-pool  --enable-autoscaling \
      --min-replicas=3 --max-replicas=6 \
      --instance-type=r5.xlarge \
      --labels=workload.sas.com/class=compute \
      --taints=workload.sas.com/class=compute:NoSchedule
```

3. Create the remaining machine pools `stateless-pool` and `stateful-pool` using the `r5.4xlarge` and `r5.2xlarge` instance types and 1 compute node replicas, adding the workload-specific labels and taints:

```shell
rosa create machinepool --cluster=$ROSA_CLUSTER_NAME \
      --name=stateful-pool --replicas=1 \
      --instance-type=r5.2xlarge \
      --labels=workload.sas.com/class=stateful \
      --taints=workload.sas.com/class=stateful:NoSchedule \
      --taints=workload.sas.com/class=stateful:NoSchedule,workload.sas.com/class=stateless:NoSchedule

rosa create machinepool --cluster=$ROSA_CLUSTER_NAME \
      --name=stateless-pool --replicas=1 \
      --instance-type=r5.4xlarge \
      --labels=workload.sas.com/class=stateless \
      --taints=workload.sas.com/class=stateless:NoSchedule \
      --taints=workload.sas.com/class=stateless:NoSchedule,workload.sas.com/class=stateful:NoSchedule
```

4. Verify your machine pools:

You can list all machine pools on your cluster or describe individual machine pools.

```shell
rosa list machinepools --cluster=sasviya
```

Sample output
```shell
ID               AUTOSCALING  REPLICAS  INSTANCE TYPE  LABELS                            TAINTS                                              AVAILABILITY ZONE  SUBNET                    VERSION  AUTOREPAIR  
default          No           2/2       m5.2xlarge                                                                                                     us-east-2a         subnet-074424179474a07d9  4.14.11  Yes         
cas-pool         No           3/3       r5d.2xlarge    workload.sas.com/class=cas        workload.sas.com/class=cas:NoSchedule                         us-east-2a         subnet-074424179474a07d9  4.14.11  Yes         
compute-pool     Yes          1/3       r5d.xlarge     workload.sas.com/class=compute    workload.sas.com/class=compute:NoSchedule                     us-east-2a         subnet-074424179474a07d9  4.14.11  Yes
stateful-pool    No           1/1       r5.2xlarge     workload.sas.com/class=stateful   workload.sas.com/class=stateful:NoSchedule,workload.sas.com/class=stateless:NoSchedule    us-east-2a         subnet-074424179474a07d9  4.14.11  Yes
stateless-pool   No           1/1       r5.4xlarge     workload.sas.com/class=stateless  workload.sas.com/class=stateless:NoSchedule,workload.sas.com/class=stateful:NoSchedule    us-east-2a         subnet-074424179474a07d9  4.14.11  Yes

```

```shell
rosa describe machinepool --cluster=sasviya cas-pool
```

Sample output
```shell
ID:                         cas-pool
Cluster ID:                 29glkgp83eut7jvmcp0ntfvqhum5p07h
Autoscaling:                No
Desired replicas:           3
Current replicas:           0
Instance type:              r5d.2xlarge
Labels:                     workload.sas.com/class=cas
Taints:                     workload.sas.com/class=cas:NoSchedule
Availability zone:          us-east-2a
Subnet:                     subnet-074424179474a07d9
Version:                    4.14.11
Autorepair:                 Yes
Tuning configs:             
Message:                    WaitingForAvailableMachines: NodeProvisioning
```

## Setting up the AWS EFS CSI Driver Operator.

1. Install the [AWS EFS CSI Driver Operator](https://github.com/openshift/aws-efs-csi-driver-operator) (a Red Hat operator).

2. If you are using AWS EFS with AWS Secure Token Service (STS), obtain a role Amazon Resource Name (ARN) for STS. This is required for installing the AWS EFS CSI Driver Operator.

3. Install the AWS EFS CSI Driver Operator.

4. Install the AWS EFS CSI Driver.

![image](https://github.com/redhat-gpst/sas-viya-rosa/assets/48925593/2df370fc-1c06-4d0d-896c-363153469d58)

## Configure AWS RDS service.

For our testing, we followed [this process](https://cloud.redhat.com/experts/rosa/sts-rds/), along with the following settings:

- PostgreSQL version 15 (do not use 16)
- Production
- Single DB instance
- Record the master username & password to specify in the SAS Viya deployment
- Instance: db.m5.xlarge, 4 vCPU, 16GB RAM
- Storage: General Purpose SSD (gp3)
- Select same VPC as ROSA, but create a new DB subnet group
 

## Configure ROSA to pull images from AWS ECR.

For our testing, we followed [this process](https://cloud.redhat.com/experts/rosa/ecr/).

## Install cert-utils-operator.

1. From OperatorHub, search on "cert utils". Click on "Cert Utils Operator". 

<img width="1118" alt="image" src="https://github.com/redhat-gpst/sas-viya-rosa/assets/48925593/d09721f2-85f0-452f-b631-50ac434b27ae">

2. Select "Install", then click on "Install".














