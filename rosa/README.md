# SAS Viya on AWS ROSA - ROSA prerequisites

[TOC]



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
I: Creating hosted CP account roles using 'arn:aws:iam::771361553200:user/open-environment-l6sw8-admin'
I: Created role 'ManagedOpenShift-HCP-ROSA-Installer-Role' with ARN 'arn:aws:iam::771361553200:role/ManagedOpenShift-HCP-ROSA-Installer-Role'
I: Created role 'ManagedOpenShift-HCP-ROSA-Support-Role' with ARN 'arn:aws:iam::771361553200:role/ManagedOpenShift-HCP-ROSA-Support-Role'
I: Created role 'ManagedOpenShift-HCP-ROSA-Worker-Role' with ARN 'arn:aws:iam::771361553200:role/ManagedOpenShift-HCP-ROSA-Worker-Role'
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
I: Creating OIDC provider using 'arn:aws:iam::771361553200:user/open-environment-l6sw8-admin'
? Create the OIDC provider? Yes
I: Created OIDC provider with ARN 'arn:aws:iam::771361553200:oidc-provider/oidc.op1.openshiftapps.com/29hejou9vg7ltren6mdsnpt2k015eqp3'

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
rosa create operator-roles --prefix "sasviya-z5sz" --oidc-config-id "29gl5vkdp33gl7qjjbrm8el2vc1oid62" --hosted-cp --installer-role-arn arn:aws:iam::771361553200:role/ManagedOpenShift-HCP-ROSA-Installer-Role
```

### STEP 6: Cluster Updates

![image](https://github.com/redhat-gpst/sas-viya-rosa/assets/48925593/dfbebe48-c8e8-4d64-9c6e-155a6a42d478)


### STEP 7: Review and Create

![image](https://github.com/redhat-gpst/sas-viya-rosa/assets/48925593/6acd55b1-4ee3-4abd-ab36-1b76bdde4c93)
![image](https://github.com/redhat-gpst/sas-viya-rosa/assets/48925593/b63dfe56-ff80-474b-991d-0a126256d0e0)
![image](https://github.com/redhat-gpst/sas-viya-rosa/assets/48925593/8e953163-847f-4b91-af33-4ed0a116f96c)















