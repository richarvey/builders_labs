# Lab 1

## kubectl
kubectl is the command line tool for interacting with your kubernetes cluster, on a posix system it store credentials in a users home directory in a folder call ```.kube``` the main config is called ```config``` this normally contains user certificates and passwords as well as cluster connection information. It's important to keep a backup of this file!

In the first lab we'll simply get this tool installed.

## Exercises

For the first exercise we will install the kubectl tool to allow you to administer kubernetes clusters on your local machine and get familiar with the commands.

### Installation on OSX

If you are running OSX we recommend that you use [HomeBrew](https://brew.sh/) to make package installation easier

```bash
brew update && brew install kubectl
```

### Installation on Linux

```bash
export KUBECTL_VERSION=1.8.3
curl -O https://storage.googleapis.com/kubernetes-release/release/v${KUBECTL_VERSION}/bin/linux/amd64/kubectl
chmod +x kubectl
sudo cp kubectl /usr/local/bin/kubectl
```

## Usage Notes

Before you can do anything meaningful with kubectl you need to have a valid configuration in order to connect to a k8s cluster. However its worth covering some of the basic operations quickly before we install minikube int he next exercise.

### Context

This is a import thing to understand, especially when running multiple clusters. Context is a referral to the cluster config thats currently active with kubectl. the following commands show you how to see what context you are using how to switch context.

```bash
kubectl config current-context              # Display the current-context
kubectl config use-context my-cluster-name  # set the default context to my-cluster-name
```

**NOTE:** its worth looking at making switch contexts easier, one tool is [kubectx](https://github.com/ahmetb/kubectx)

### Getting help

You can simply run:

```bash
kubectl
```

To return you a list of all available commands that kubectl provides:

```bash
Basic Commands (Beginner):
  create         Create a resource from a file or from stdin.
  expose         Take a replication controller, service, deployment or pod and expose it as a new Kubernetes Service
  run            Run a particular image on the cluster
  set            Set specific features on objects
  run-container  Run a particular image on the cluster. This command is deprecated, use "run" instead

Basic Commands (Intermediate):
  get            Display one or many resources
  explain        Documentation of resources
  edit           Edit a resource on the server
  delete         Delete resources by filenames, stdin, resources and names, or by resources and label selector

Deploy Commands:
  rollout        Manage the rollout of a resource
  rolling-update Perform a rolling update of the given ReplicationController
  scale          Set a new size for a Deployment, ReplicaSet, Replication Controller, or Job
  autoscale      Auto-scale a Deployment, ReplicaSet, or ReplicationController

Cluster Management Commands:
  certificate    Modify certificate resources.
  cluster-info   Display cluster info
  top            Display Resource (CPU/Memory/Storage) usage.
  cordon         Mark node as unschedulable
  uncordon       Mark node as schedulable
  drain          Drain node in preparation for maintenance
  taint          Update the taints on one or more nodes

Troubleshooting and Debugging Commands:
  describe       Show details of a specific resource or group of resources
  logs           Print the logs for a container in a pod
  attach         Attach to a running container
  exec           Execute a command in a container
  port-forward   Forward one or more local ports to a pod
  proxy          Run a proxy to the Kubernetes API server
  cp             Copy files and directories to and from containers.
  auth           Inspect authorization

Advanced Commands:
  apply          Apply a configuration to a resource by filename or stdin
  patch          Update field(s) of a resource using strategic merge patch
  replace        Replace a resource by filename or stdin
  convert        Convert config files between different API versions

Settings Commands:
  label          Update the labels on a resource
  annotate       Update the annotations on a resource
  completion     Output shell completion code for the specified shell (bash or zsh)

Other Commands:
  api-versions   Print the supported API versions on the server, in the form of "group/version"
  config         Modify kubeconfig files
  help           Help about any command
  plugin         Runs a command-line plugin
  version        Print the client and server version information

```

If you want specific help on a given command run:

```bash
kubectl <command> --help
```

### Getting information

```bash
kubectl get pods
```

returns all pods running in the default namespace

```bash
kubectl -n application-a get pods
```

This returns the pods in a given namespace

The get syntax is able to retrieve information on many parts of a k8s system, a full list is below.

- all
- certificatesigningrequests (aka 'csr')
- clusterrolebindings
- clusterroles
- clusters (valid only for federation apiservers)
- componentstatuses (aka 'cs')
- configmaps (aka 'cm')
- controllerrevisions
- cronjobs
- customresourcedefinition (aka 'crd')
- daemonsets (aka 'ds')
- deployments (aka 'deploy')
- endpoints (aka 'ep')
- events (aka 'ev')
- horizontalpodautoscalers (aka 'hpa')
- ingresses (aka 'ing')
- jobs
- limitranges (aka 'limits')
- namespaces (aka 'ns')
- networkpolicies (aka 'netpol')
- nodes (aka 'no')
- persistentvolumeclaims (aka 'pvc')
- persistentvolumes (aka 'pv')
- poddisruptionbudgets (aka 'pdb')
- podpreset
- pods (aka 'po')
- podsecuritypolicies (aka 'psp')
- podtemplates
- replicasets (aka 'rs')
- replicationcontrollers (aka 'rc')
- resourcequotas (aka 'quota')
- rolebindings
- roles
- secrets
- serviceaccounts (aka 'sa')
- services (aka 'svc')
- statefulsets
- storageclasses

### Creating resources

You can use kubectl in a couple of ways to create resources, the first way would be specify all your arguments on the cli but a more common way is to use a k8s json or yaml file to specify your resources and pass it into the create command.

```bash
kubectl create -f my-service.yaml
```

### Deleting resources

This is a the exact same syntax as the create command. To delete everything you've created in the yaml or json file run the command:

```bash
kubectl delete -f my-service.yaml
```

You can also delete individual resources such as a pod:

```bash
kubectl delete pod <pod_name>
```

## Resources

- kubectl cheat sheet - [link](https://kubernetes.io/docs/user-guide/kubectl-cheatsheet/)
- kubectx - [kubectx](https://github.com/ahmetb/kubectx)

## Exercises

- Lab 1: [Installing k8s tools](/kubernetes-101/labs/00-tools.md)
- Lab 2: [Install Minikube](/kubernetes-101/labs/01-minikube.md)
- Lab 3: [Basic tool usage](/kubernetes-101/labs/02-basic-usage.md)
- Lab 4: [Intermediate tool usage](/kubernetes-101/labs/03-intermediate-usage.md)

##### Labs : [kubernetes-101](/kubernetes-101/) | [kubernetes-201](/kubernetes-201/) | [kubernetes-301](/kubernetes-301/)
