<!--
ignore these words in spell check for this file
// cSpell:ignore udemy Microservices Kubeadm replicaset replicasets Mesos kubectl kodeKloud katakoda systeminfo virtualbox vmwarefusion hyperv vmware podman
-->

# Kubernetes for the Absolute Beginners - Hands-on

based on the udemy course [Kubernetes for the Absolute Beginners - Hands-on](https://www.udemy.com/course/learn-kubernetes/).

TOC

- [Kubernetes for the Absolute Beginners - Hands-on](#kubernetes-for-the-absolute-beginners---hands-on)
  - [Kubernetes Overview](#kubernetes-overview)
  - [Setup Kubernetes](#setup-kubernetes)
  - [Kubernetes Concepts](#kubernetes-concepts)
  - [YAML Introduction](#yaml-introduction)
  - [Kubernetes Concepts - PODs, ReplicaSets, Deployments](#kubernetes-concepts---pods-replicasets-deployments)
    - [Pods](#pods)
      - [lab access with kodekloud](#lab-access-with-kodekloud)
    - [Kubernetes Controllers: ReplicaSet](#kubernetes-controllers-replicaset)
  - [Networking in Kubernetes](#networking-in-kubernetes)
  - [Services](#services)
  - [Microservices Architecture](#microservices-architecture)
  - [Kubernetes on Cloud](#kubernetes-on-cloud)
  - [Setup Multi Node Cluster Using Kubeadm](#setup-multi-node-cluster-using-kubeadm)
  - [Extra Takeaways](#extra-takeaways)



## Kubernetes Overview

<details>
<summary>
What are Containers, what is Orchestration and what is Kubernetes?
</summary>

understanding kubernetes requirers understanding containerization and orchestration. 

**Containerization** is a way to store all the requirements and dependencies, we want to avoid problems with conflicting versions, operating systems and environments. using containers allows to isolate components. each container is a completely isolated environment, with code, libraries and dependencies. docker is such a container program. docker allows to run different containers while sharing the same OS kernel. this is the difference between using docker and using virtual machine.

when we want to run a windows software on a linux os, we can use a windows containers services, and vice versa. a virtual machine uses a hyper visor to manage the different software, this requires an entire OS, while docker needs less resources.

we can images of many popular software on the dockerHub registry, this includes nodeJs, databases, servers, etc...\

an image a a template, we can create many containers based on the same image, a container adds a 'file system' on top of the image, so all the containers share the common parts of the image, but each can change the individual 'files' inside it.

when we create an image, we build everything inside it, so it should work the same way no matter where it's running.

**Container Orchestration** allows us to manage containers, deploy them, scale the amount of each container, and control tht networking. kubernetes is one orchestration technology, but there is also Docker-Swarm or apache Mesos. kubernetes gives us scaling, container networking and communication, and reliability.

**Kubernetes Architecture** is based upon resources and other basic concepts.

- node - a machine (real or virtual), where kubernetes is installed. it can be a worker node (called Minion in the past) or manager node.
- cluster - a set of nodes running together.
- master node -  a managing node

kubernetes components:
- API server - the front end for kubernetes, kubectl and other command communicate with the api server.
- etcd - key:value store. the one source of truth for master nodes
- kubelet - the agent that runs on each node in the cluster.
- Container runtime - how we run container, like docker.
- Controller - noticing and responding to worker nodes events.
- Scheduler - distributing work amongst worker nodes.

master nodes vs worker nodes:\
worker nodes holds the containers themselves which run the image. a worker node has the kubelet agents.
the master has api server, etcd database, the controller and the scheduler. master nodes work together to manage worker nodes.


KubeCTL is the command-line tool we can use to control the kubernetes cluster.

- kubectl run
- kubectl cluster-info
- kubectl get

</details>

## Setup Kubernetes

<details>
<summary>
Setting up kubernetes locally - kubectl and minikube.
</summary>

setting up kubernetes, we can do it locally, with minikube, microK8s or kubeadm. we can also set it up on a cloud provider service, like google, aws or azure. in this course we can use ~~[kodeKloud](www.kodeKloud.com)~~ (katakoda).

the easiest way to start is by using minikube. minikube bundles all the components of a node (manager and worker) into one image. we can then run this image from our hypervisor(such as virtual box, but we can also use docker runtime), we then use kubectl to control minikube.

there is also the hands-on lab on katakoda, where we can practice some kubectl commands.


demo of installing minikube. following instructions on the sites. enabling virtualization

```sh
kubectl version
systeminfo
minikube start --driver="virtualbox |vmwarefusion|hyperv|vmware|docker|podman (experimental)|ssh"
minikube status
minikube stop
minikube delete
```

when we run a minikube, the kubectl commands will be directed to it.
</details>

## Kubernetes Concepts

<details>
<summary>
Basic Kubernetes - Pods
</summary>

understanding Kubernetes Pods. we assume that we have a kubernetes cluster running, and that the application image was uploaded to a registry.

in kubernetes, the smallest unit is the pod. A pod wraps a container (one or more). when we want to scale up, we add additional pods with the same containers.

there are cases when pods have more than one container, but it shouldn't be the same container. we can have additional, helper containers inside the same pod. containers inside the same pod can communicate with one another freely by using localhost, and they share storage space.

we could try to manage this with *docker container* commands, but that would be a huge hassle, sticking related containers into pods makes things much simpler.


Demo

```sh
minikube start
kubectl run nginx --image=nginx
kubectl get pods
kubectl describe pod nginx
kubectl get pods -o wide
```
</details>

## YAML Introduction

<details>
<summary>
Understanding Basic Yaml
</summary>

Understanding yaml. comparison between xaml, json and yaml. yaml uses *key: value* pairs (the space matters). arrays are lists with *-* to specify each element, dictionaries are nested key-values pairs under an outer key. we can have dictionaries, lists(arrays) and list of dictionaries. lists are ordered, dictionaries are unordered.

comments are denoted with a hash symbol

```yaml
key: value
array:
  - list_item1
  - list_item2
  - list_item3
dictionary:
  key1: value1
  key2: value2
  key2: value3

#list of dictionaries, keys are in the same level
list_of_dictionaries:
  - property1: value1
    property2: value2
  - property1: value1
    property2: value2
    property3: value3
  - property1: value1
    property2: value2

#list of objects
list_of_objects:
  - object1:
      property1: value1
      property2: value2
  - object2:
      property1: value3
      property2: value4
# comment!
```


now we can look at the coding exercises (1-6).




</details>

## Kubernetes Concepts - PODs, ReplicaSets, Deployments

<!-- <details> -->
<summary>
Yaml files for kubernetes resources.
</summary>


### Pods

each kubernetes yaml file has four top level fields (root keys)
- apiVersion
- kind
- metadata
- spec

we start with creating a yaml file for a pod, we start with the apiVersion and the kind root keys, for the metadata we add the name and labels. the keys under **metadata** are fixed, but under **labels** we can specify everything. the spec key is different for each kind of resource. for pods we define containers:

lets create a pod resource, we name the file "pod-definition.yaml"
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myApp-pod
  labels:
    app: myApp
    #tier: frontend
spec:
  containers:
    - name: nginx-container
      image: nginx
```

we can now use **kubectl** to create the pod and get data about it.

```sh
kubectl create -f pod-definition.yaml
kubectl get pods
kubectl describe pod myApp-pod
```

rather than use a regular text editor, we can use an IDE that has support for yaml files, and use a extension to get hints about kubernetes format. we get some validations for formats and required fields.



#### lab access with kodekloud

create a kodeKloud account, use the coupon, and start 

lab getting familiarized:
```sh
kubectl get nodes
kubectl version
kubectl get nodes -o wide
```

lab pods and yaml:
```sh
kubectl get pods
kubectl run nginx --image=nginx
kubectl get pods -o wide
kubectl describe pod <pod name>
kubectl describe pod webapp
kubectl delete pod webapp

#generate this for us
#kubectl run redis --image=redis123 --dry-run=client -o yaml > redis.yaml
touch redis.yaml
vi redis.yaml
#quit vim with :wq!
kubectl create -f redis.yaml
#vi redis.yaml

#kubectl apply -f redis.yaml
kubectl edit pod redis
#vi redis.yaml
```

### Kubernetes Controllers: ReplicaSet

having more than one instance of the same pod, if a pod crushes then we bring a new one up, we can also have load balancing across pods and nodes.

replication controller and replicaSet. replicaSet is the new implementation of the same idea. 

let's look at the yaml for a replication Controller, in the *template* we can simply bring in the contents from the pod.yaml file.

```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: myApp-rc
  labels:
    app: myApp
    type: front-end
spec:
  template:
    metadata:
      name: myApp-pod
      labels:
        app: myApp
        type: front-end
    spec:
      containers:
        -
          name: nginx-container
          image: nginx
  replicas: 3
```
and we create it with kubectl
```sh
kubectl create -f rc-dentition.yaml
kubectl get replicationController
```

but if we want a replicaSet (which we want), we need a different yaml file. now we must define the selector. we can also monitor pods that weren't created by it, and thats why we use the selector. the template labels and the selectors must match.


```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: myApp-replicaSet
  labels:
    app: myApp
    type: front-end
spec:
  template:
    metadata:
      name: myApp-pod
      labels:
        app: myApp
        type: front-end
    spec:
      containers:
        -
          name: nginx-container
          image: nginx
  replicas: 3
  selector:
    matchLabels:
      type: front-en   
```

if we want to scale the replicaSet, we can change the number in the yaml and run the *replace* command, or run the scale command.

```sh
kubectl replace -f replicaSet-definition.yaml
kubectl scale --replicas=6 -f replicaSet-definition.yaml
kubectl scale --replicas=6 replicaset myApp-replicaset
```

other commands
```sh
kubectl create
kubectl replace
kubectl delete
kubectl scale
kubectl describe
kubectl edit
```



</details>

## Networking in Kubernetes

<!-- <details> -->
<summary>

</summary>
</details>

## Services

<!-- <details> -->
<summary>

</summary>
</details>

## Microservices Architecture

<!-- <details> -->
<summary>

</summary>
</details>

## Kubernetes on Cloud

<!-- <details> -->
<summary>

</summary>
</details>

## Setup Multi Node Cluster Using Kubeadm

<!-- <details> -->
<summary>

</summary>
</details>

## Extra Takeaways

<!-- <details> -->
<summary>
Special notes to remember.
</summary>
</details>

