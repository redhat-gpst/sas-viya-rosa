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

