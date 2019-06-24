# Lab 2

## Exercise build a cluster

In this lab we will build a cluster, we will see the creation of three InstanceGroups for the masters, one in each AZ and the creation of an InstanceGroup for Nodes spanning the three AZ's. The diagram below shows the reference architecture we are heading for. The ELB that fronts the API servers (masters) will be deployed into the private subnet, so we to ensure we have access to this network either via a VPN or Direct Connect.

![AWS kops](kubernetes-201/labs/img/deployment.png "Figure. 1")
(Figure 1: AWS reference deployment Architecture)

### Run kops create

The follow command will create all the files required for the deployment. It wont actually create the cluster unless you add --yes to the end. However we want to tweak the install further so we wont add this flag. This method will not use the default kops method which creates a new VPC, subnets, and NAT gateways, but it will instead allow you to deploy into an existing VPC.

Lets start by setting up some env variables to make running the comands easier:

```bash
export S3_BUCKET=s3://your-bucket-name
export CLUSTER_NAME=your-cluster-fqdn
export VPC_ID=your_target_vpc
```

Now lets create the cluster config:

```bash
kops create cluster \
--admin-access 172.31.0.0/12 \
--api-loadbalancer-type internal \
--cloud aws \
--cloud-labels "ProductCode=PRD298,Environment=dev,InventoryCode=combined-kops" \
--networking calico \
--topology private \
--zones=${ZONES} \
--encrypt-etcd-storage \
--kubernetes-version ${VERSION} \
--dns private ${FQDN} \
--dns-zone ${TLD} \
--node-size m4.large \
--node-count=3 \
--node-volume-size 100 \
--master-zones=${MASTER-ZONES} \
--master-size m4.large \
--master-volume-size 100 \
--vpc=${VPC_ID} \
--state={$S3_BUCKET}
```

These are the values you will need to edit:

**zones:** This defines where your nodes get deployed to in their ASG
example:
**--zones=eu-west-1a,eu-central-1b,eu-central-1c**

**kubeneretes-version:** Self explanatory
example:
**--kubernetes-version 1.7.5**

**dns:** This specifies the FQDN of your cluster and also specifies that the DNS zone is Private
example:
**--dns private kops.contain.io**

**dns-zone:** the TLD of the zone
example:
**--dns-zone contain.io**

**master-zones:** These are you AWS AZ's if you specify three you'll get a master in each AZ for HA.
example:
**--master-zones=eu-west-1a,eu-central-1b,eu-central-1c**

**vpc:** Set this to your target VPC ID
example:
**--vpc=vpc-e5fFGHfrjh**

**state:** This is the S3 bucket you just created
example:
**--state=s3://my-kops-configs**

Once this command runs it will place files in S3 that are then used by the S3 command in the future to make further edits. You can of course tweak other values to customise the deployment.

As we stated before we don't want to allow kops to default and create a new VPC or to automatically select subents for us, so we are going to go in and edit the cluster settings because even though we've told kops to use an existing VPC its still tried to guess which subnets we want to use. In a simple network this is fine but if you have multiple private subnets its likely to get this incorrect. Lets edit the cluster settings:

```bash
kops edit cluster --name=${CLUSTER_NAME} --state=${S3_Bucket}
```

There will be a section called subnets and this will contain 6 subnets by default, private and utility subnets. Private are where your masters are deployed and utility are for nodes. We are going to go ahead and edit these by changing the subenet-ids and the cidr blocks to match where we want to deploy to and then we'll add the address of the ANT gateways (egress). You'll end up with something like the following:

```
subnets:
 - cidr: 10.199.24.0/26
   egress: nat-012345678910
   id: subnet-12345
   name: eu-west-1a
   type: Private
   zone: eu-west-1a
 - cidr: 10.199.24.64/26
   egress: nat-109876543210
   id: subnet-56789
   name: eu-west-1b
   type: Private
   zone: eu-west-1b
 - cidr: 10.199.24.128/26
   egress: nat-201918171615
   id: subnet-101112
   name: eu-west-1c
   type: Private
   zone: eu-west-1c
 - cidr: 10.199.25.128/27
   id: subnet-131415
   name: utility-eu-west-1a
   type: Utility
   zone: eu-west-1a
 - cidr: 10.199.25.160/27
   id: subnet-161718
   name: utility-eu-west-1b
   type: Utility
   zone: eu-west-1b
 - cidr: 10.199.25.192/27
   id: subnet-192021
   name: utility-eu-west-1c
   type: Utility
   zone: eu-west-1c
```

Save this file and now you are ready to deploy, you can verify the actions taken by running:

```bash
kops update cluster --name=${CLUSTER_NAME} --state=${S3_Bucket}
```

Then to commit the changes run the same command with --yes on the end:

```bash
kops update cluster --name=${CLUSTER_NAME} --state=${S3_Bucket} --yes
```

This will take a few minuites for cluster to spin up all th einstances and for the components to automatically configure.

## Verify the cluster

There are two ways you can validate the cluster is built correctly. The first is we can use the kops command:

```bash
kops validate cluster --name=${CLUSTER_NAME} --state=${S3_Bucket}
```

This will show us the status of the instances in the Instance groups and the core k8s compoents. However we may wish to test our connectivity to the API and that our kube config is correctly configured. You should of seen at the end of the cluster build a message stating that the *current context* has been updated for kubectl, however you can confirm this has happened by running ```kubectx```. If everything is fine you'll now see that your current contect is the name of your new cluster. Lets try and connect to the API gateway with kubectl:

```bash
kubectl get nodes -o wide
```

This should show you that the cluster is up and running and that you have 6 nodes in total. Three with the role of master and 3 with the role of node.

## Exercises

- Lab 1: [Installing kops](/kubernetes-201/labs/00-install-kops.md)
- Lab 2: [Deploy a cluster](/kubernetes-201/labs/01-deploy-cluster.md)
- Lab 3: [Addons](/kubernetes-201/labs/02-addons.md)
- Lab 4: [Deploy a Stateless Application](/kubernetes-201/labs/03-deploy-service.md) | [Deploy a Stateful Application](/kubernetes-201/labs/03-deploy-stateful-service.md)
- Lab 5: [Upgrade a cluster](/kubernetes-201/labs/04-upgrading.md)

##### Labs : [kubernetes-101](/kubernetes-101/) | [kubernetes-201](/kubernetes-201/) | [kubernetes-301](/kubernetes-301/)
