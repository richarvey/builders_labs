# Lab 4

## Intermediate Usage

If you want to try some debug of a container. Logs and exec are very useful commands.

### 1. Logs

You can tail logs from containers pretty easily. kubectl allows you to do this right from you desktop without having to hop onto the host.

First locate your container

```bash
kubectl -n my-namespace get po
```

make a note of the container name, then run:

```bash
kubectl -n my-namespace logs <container-name>
```

you can also follow the logs with:

```bash
kubectl -n my-namespace logs -f <container-name>
```

### 2. Exec

Exec lets you run a command in a container much like the ```docker exec``` command. Once again first find your conatiner name:

```bash
kubectl -n my-namespace get po
```

make a note of the container name, then run:

```bash
kubectl -n my-namespace exec -it <container-name> <command>
```

the ```-it``` flag makes the session interactive so if running bash you can debug a container from within.

## Exercises

- Lab 1: [Installing k8s tools](/kubernetes-101/labs/00-tools.md)
- Lab 2: [Install Minikube](/kubernetes-101/labs/01-minikube.md)
- Lab 3: [Basic tool usage](/kubernetes-101/labs/02-basic-usage.md)
- Lab 4: [Intermediate tool usage](/kubernetes-101/labs/03-intermediate-usage.md)

##### Labs : [kubernetes-101](/kubernetes-101/) | [kubernetes-201](/kubernetes-201/) | [kubernetes-301](/kubernetes-301/)
