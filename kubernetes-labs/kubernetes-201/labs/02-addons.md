# Lab 3

## Exercises

Deployments of extra services

### Deploying services to the cluster

Its useful to deploy some extra services to the cluster, in this lab we'll cover off:

- Monitoring via heapster
- The Dashboard
- Helm
- Autoscaler

#### Heapster:

Heapster will bring you system usage metrics which are crucial for monitoring and keeping an eye on what resources a pod is using. Its worth noting that its best to bring up heapster before the dashboard to ensure you get the graphs in the dashboard.

```bash
kubectl create -f https://raw.githubusercontent.com/kubernetes/kops/master/addons/monitoring-standalone/v1.7.0.yaml
```
_Note: 1.7.0 was the latest at time of writing, there may be newer versions available_


#### DashBoard:

The dash board project gives you a visual representation of whats happening in your cluster. It also allows you to deploy and edit resources.

```bash
kubectl create -f https://raw.githubusercontent.com/kubernetes/kops/master/addons/kubernetes-dashboard/v1.7.0.yaml
```
_Note: 1.7.0 was the latest at time of writing, there may be newer versions available_

To get your dashboard password run:

```bash
kubectl config view --minify
```

You should then be able to access the dashboard at the Route53 pointer to the API ELB:
  `https://api.${CLUSTER_NAME}/ui/`


#### Helm:

Helm describes its self as "the package manager for kubernetes". It allows you to have versionable deployments which you can easily upgrade or roll back, as well as providing flexibility in templating different environments for the same deployment.

Download a binary release of the Helm client. You can use tools like homebrew, or look at the official [releases page](https://github.com/kubernetes/helm/releases).

Install helm on your system and run:

```bash
helm init
```

This deploys tiller on your cluster and you can check its running by issuing the command:

```bash
kubectl get po -n kube-system | grep tiller
```

The helm documentation can be found [here](https://helm.sh/)

#### Autoscaler:

The autoscaler will scale EC2 instances fro you should kubernetes start reporting it can't place pod's because of lack of resources.

Before installing the auto scaller we need to change your KOPS InstanceGroup for the nodes to change the min and max nodes:

```bash
kops edit ig nodes --name=${CLUSTER_NAME} --state=${S3_BUCKET}
```

This will allow the ASG to scale, by default both values are 3. Now commit the changes:

```bash
kops update cluster --name=${CLUSTER_NAME} --state=${S3_BUCKET}
```

Now prepare the following ```cluster-autoscaler.sh``` script and change the following MIN_NODES, MAX_NODES and GROUP_NAME to match your settings:

```
CLOUD_PROVIDER=aws
IMAGE=gcr.io/google_containers/cluster-autoscaler:v0.6.0
MIN_NODES=3
MAX_NODES=15
AWS_REGION=eu-west-1
GROUP_NAME="nodes.k8s.example.com"
SSL_CERT_PATH="/etc/ssl/certs/ca-certificates.crt" # (/etc/ssl/certs for gce)

addon=cluster-autoscaler.yml
wget -O ${addon} https://raw.githubusercontent.com/kubernetes/kops/master/addons/cluster-autoscaler/v1.6.0.yaml

sed -i.bak -e "s@{{CLOUD_PROVIDER}}@${CLOUD_PROVIDER}@g" "${addon}"
sed -i.bak -e "s@{{IMAGE}}@${IMAGE}@g" "${addon}"
sed -i.bak -e "s@{{MIN_NODES}}@${MIN_NODES}@g" "${addon}"
sed -i.bak -e "s@{{MAX_NODES}}@${MAX_NODES}@g" "${addon}"
sed -i.bak -e "s@{{GROUP_NAME}}@${GROUP_NAME}@g" "${addon}"
sed -i.bak -e "s@{{AWS_REGION}}@${AWS_REGION}@g" "${addon}"
sed -i.bak -e "s@{{SSL_CERT_PATH}}@${SSL_CERT_PATH}@g" "${addon}"

kubectl apply -f ${addon}
```

The make the script executable and then run it:

```bash
chmod +x cluster-autoscaler.sh
./cluster-autoscaler.sh
```

## Exercises

- Lab 1: [Installing kops](/kubernetes-201/labs/00-install-kops.md)
- Lab 2: [Deploy a cluster](/kubernetes-201/labs/01-deploy-cluster.md)
- Lab 3: [Addons](/kubernetes-201/labs/02-addons.md)
- Lab 4: [Deploy a Stateless Application](/kubernetes-201/labs/03-deploy-service.md) | [Deploy a Stateful Application](/kubernetes-201/labs/03-deploy-stateful-service.md)
- Lab 5: [Upgrade a cluster](/kubernetes-201/labs/04-upgrading.md)

##### Labs : [kubernetes-101](/kubernetes-101/) | [kubernetes-201](/kubernetes-201/) | [kubernetes-301](/kubernetes-301/)
