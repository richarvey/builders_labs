# Lab 5

## Exercises

kops allows you to easily upgrade the version of kubernetes and the underlaying AMI (ec2 instance).

### 1. Upgrading Kubernetes

At the time of writing this document the kops tool by default builds you a kubernetes 1.7.5 cluster, however 1.7.8 is available. We can use kops to instruct the cluster to use the later version. In this exercise we'll do that.

kops has a new feature that drains nodes and validates their status before upgrading the next node. This is a very useful feature and ensure stability in your cluster whilst you are upgrading. To enable this feature run the following:

```bash
export KOPS_FEATURE_FLAGS="+DrainAndValidateRollingUpdate"
```

No wits time to edit the cluster definition:

```bash
kops edit cluster --name=${CLUSTER_NAME} --state=${S3_Bucket}
```
Find and set the ```KubernetesVersion``` to the target version (e.g. v1.7.8), once you've changed this save the file and exit. We are now ready to commit these changes to the cluster:

```bash
kops update cluster --name=${CLUSTER_NAME} --state=${S3_Bucket}  # to preview changes
kops update cluster --name=${CLUSTER_NAME} --state=${S3_Bucket} --yes
kops rolling-update cluster --name=${CLUSTER_NAME} --state=${S3_Bucket} --yes
```

The rolling update command will now evacuate all the pods from the hosts onto other nodes, mark the node to stop new containers being created there, and when its happy everything has moved it will instruct AWS to stop the instance and replace it with a new one, which will contain the new configuration. If you watch the status of the nodes you'll see this happening and you'll see when a new node starts it'll be running kubernetes 1.7.8.

```bash
watch kubectl get no -o wide
```

### 2. Upgrading the EC2 Instances/AMI

As we rolled the cluster with the latest version of the kops debian image its pretty hard to do this update, however we can swap out debian and use coreOS instead. We'll first need to get the AMI-id of the latest coreOS image. This handy bash function can be used to get the ID for any region, add it to your .bash_profile / .bashrc and restart your terminal session:

```bash
coreos() {
    if [ "$1" == "" ]; then
        echo "please specify a regions (eg: eu-west-1)"
    else
        aws ec2 describe-images --region=$1 --owner=595879546273 --filters "Name=virtualization-type,Values=hvm" "Name=name,Values=CoreOS-stable*" --query 'sort_by(Images,&CreationDate)[-1].{id:ImageId}'
    fi
}
```

Now when you run the following command you will get the latest AMI-id back that you can use with kops:

```bash
coreos eu-west-1
```

Enable the validate and drain feature for rolling updates:

```bash
export KOPS_FEATURE_FLAGS="+DrainAndValidateRollingUpdate"
```

List your instanceGroups as we'll need to upgrade them all:

```bash
kops validate cluster --name=${CLUSTER_NAME} --state=${S3_Bucket}
```

Now update each instance group you have, for example:

```bash
kops edit ig master-eu-west-1a --name=${CLUSTER_NAME} --state=${S3_Bucket}
kops edit ig master-eu-west-1b --name=${CLUSTER_NAME} --state=${S3_Bucket}
kops edit ig master-eu-west-1c --name=${CLUSTER_NAME} --state=${S3_Bucket}
kops edit ig nodes --name=${CLUSTER_NAME} --state=${S3_Bucket}
```

change the AMI-id in each of the above, for the one you generated with the ```coreos``` command.

Now commit the changes to the cluster:

```bash
kops update cluster --name=${CLUSTER_NAME} --state=${S3_Bucket}  # to preview changes
kops update cluster --name=${CLUSTER_NAME} --state=${S3_Bucket} --yes
kops rolling-update cluster --name=${CLUSTER_NAME} --state=${S3_Bucket} --yes
```

This will start the upgrade process and once finsihed you will see the nodes are running coreOS and not debian as before. you can see this by looking at the output of:

```bash
kubectl get no -o wide
```

## Exercises

- Lab 1: [Installing kops](/kubernetes-201/labs/00-install-kops.md)
- Lab 2: [Deploy a cluster](/kubernetes-201/labs/01-deploy-cluster.md)
- Lab 3: [Addons](/kubernetes-201/labs/02-addons.md)
- Lab 4: [Deploy a Stateless Application](/kubernetes-201/labs/03-deploy-service.md) | [Deploy a Stateful Application](/kubernetes-201/labs/03-deploy-stateful-service.md)
- Lab 5: [Upgrade a cluster](/kubernetes-201/labs/04-upgrading.md)

##### Labs : [kubernetes-101](/kubernetes-101/) | [kubernetes-201](/kubernetes-201/) | [kubernetes-301](/kubernetes-301/)
