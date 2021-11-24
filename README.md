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
      - [lab access with kodekloud](#lab-access-with-kodekloud)
    - [Pods](#pods)
      - [Lab - pods and yaml](#lab---pods-and-yaml)
    - [Kubernetes Controllers: ReplicaSet](#kubernetes-controllers-replicaset)
      - [Lab - Replica Set](#lab---replica-set)
    - [Deployments](#deployments)
      - [Lab - Deployment](#lab---deployment)
      - [Deployment update and rollback](#deployment-update-and-rollback)
      - [Lab - Rollout and Updates](#lab---rollout-and-updates)
  - [Networking in Kubernetes](#networking-in-kubernetes)
  - [Services](#services)
      - [Lab - Services](#lab---services)
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

<details>
<summary>
Yaml files for kubernetes resources: pods, replicaSet and Deployments
</summary>

#### lab access with kodekloud

create a kodeKloud account, use the coupon, and start 

lab getting familiarized:
```sh
kubectl get nodes
kubectl version
kubectl get nodes -o wide
```


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
  name: myapp-pod
  labels:
    app: myapp
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
kubectl describe pod myapp-pod
```

rather than use a regular text editor, we can use an IDE that has support for yaml files, and use a extension to get hints about kubernetes format. we get some validations for formats and required fields.



#### Lab - pods and yaml

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
  name: myapp-rc
  labels:
    app: myapp
    type: front-end
spec:
  template:
    metadata:
      name: myapp-pod
      labels:
        app: myapp
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
  name: myapp-replicaSet
  labels:
    app: myapp
    type: front-end
spec:
  template:
    metadata:
      name: myapp-pod
      labels:
        app: myapp
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
kubectl scale --replicas=6 replicaset myapp-replicaset
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

#### Lab - Replica Set

```sh
kubectl get pods
kubectl get replicaset
kubectl describe replicaset new-replica-set
kubectl describe pod new-replica-set-<pod-name>
kubectl delete pod new-replica-set-<pod-name>
kubectl create -f replicaset-definition-1.yaml
# edit, change key from v1 to apps/v1
kubectl create -f replicaset-definition-1.yaml

kubectl create -f replicaset-definition-2.yaml --dry-run=server
# edit, change selector to match labels
kubectl create -f replicaset-definition-2.yaml

kubectl delete replicaset replicaset-1
kubectl delete -f replicaset-definition-2.yaml

kubectl edit replicaset new-replica-set
kubectl delete pod -l name=busybox-pod
kubectl scale --replicas=5 rs/new-replica-set
kubectl scale --replicas=2 rs/new-replica-set
```

### Deployments

deploying a cluster in an environment. upgrading (rolling upgrade), roll backs, applying changes at once.
- pods - single instances
- replicaSet - multiple pods
- deployment - higher level


a deployment creates a replicaSet, which in turn creates(or controls) pods. but it has additional functionalities.

deployment-definition.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deployment
  labels:
    app: myapp
    type: front-end
spec:
  replicas: 3
  selector:
    matchLabels:
      type: front-end
  template:
    metadata:
      name: myapp-pod
      labels:
        app: myapp
        type: front-end
    spec:
      containers:
      - name: nginx-container
        image: nginx

```
we can create it and see how everything was created
```sh
kubectl create -f deployment-definition.yaml
kubectl get deployment
kubectl get replicaset
kubectl get pods
#kubectl get deployments, replicasets, pods
#kubectl get all
```


#### Lab - Deployment

```sh
kubectl get pods
kubectl get replicaset
kubectl get deployment

kubectl describe deployment frontend-deployment
kubectl describe pod frontend-deployment-<pod-name>

#didn't work, because of the "kind"
kubectl create -f deployment-definition-1.yaml --dry-run=server
#change file, bad case in "kind: deployment"
kubectl create -f deployment-definition-1.yaml


# get basic form
kubectl create deployment httpd-frontend --image httpd:2.4-alpine -o yaml --dry-run=client > my_deployment.yaml
vi my_deployment.yaml
kubectl create -f my_deployment.yaml
```

#### Deployment update and rollback

rollout an versioning, each rollout creates a revision to the deployment, we can see the current status, or see the history.

```sh
kubectl rollout status deployment/myapp-deployment
kubectl rollout history deployment myapp-deployment
```

when we upgrade the version, we can take down all the pods and then create the pods with the new versions. this is called the **recreate** strategy, and it's not great because it creates a downtime period in which no pods are running. a better idea is a **rolling update** strategy, that removes a pod and creates a pod one by one.


if we make a change to a deployment file, we can trigger an update with `kubectl apply`. if we simply want to change the image, we an use a `set` command (which will also trigger a rollout), but this will mean our file isn't relevant anymore.

```sh
kubectl apply -f deployment-definition.yaml 
kubectl set image deployment/myapp-deployment nginx=nginx:1.9.1
```

when we use different update strategies, we can use `kubectl describe` to see the process of replacing the pods.

if we want to rollback the deployment to a earlier version, we can `undo` the rollout.

```sh
kubectl rollout undo deployment myapp-deployment
```

when we create or edit a deployment, we can add the *--record* flag. that will add the command to the annotations which we can see when we `describe` the resource. 


#### Lab - Rollout and Updates

```sh
kubectl get pods,service
./curl-test.sh
kubectl get pods
kubectl describe deployment/frontend
kubectl set image deployment/frontend simple-webapp=kodekloud/webapp-color:v2
./curl-test.sh

kubectl edit deployment/frontend
kubectl set image deployment/frontend simple-webapp=kodekloud/webapp-color:v3
./curl-test.sh

kubectl rollout status deployment frontend

```
</details>

## Networking in Kubernetes

<details>
<summary>
Networking basics.
</summary>

basics of networking. a node has an ip address (or the minikube vm has this address). with a single-node, single pod,  each pod gets an ip address (unlike docker, where each container gets an ip). the kubernetes configures a private network for the pods inside it, this means pods can talk to one another, this can be done via ip addresses, but it's not a good idea (ips can change).

if we have multiple nodes, each node has a different ip address, but we need to configure the networking so that pods won't have the same ips (even if they don't share the same network because the are in different nodes)

we need to see up the networking to meet the requirements:
> - All containers/POds can communicate to one another without NAT.
> - All nodes can communicate with all containers and vice-versa without NAT.

there are pre-build solutions that do this for us (such as flannel, cilium, nsx), this depends on the platform in which we. a virtual network is created and uses routing to control communications.


</details>

## Services

<details>
<summary>
Kubernetes Services for networking
</summary>

services allow communication between components in the cluster, between each other and communication from the outside world. we can connect the frontend, backend and the database, therefore achieving decoupling.


the nodes control the network in which the pods exist in, and we want to expose the pods to the outside without requiring an ssh.

kubernetes services are a type of resource, just like deployments and pods.

types:
- ClusterIP
- NodePort
- LoadBalancer

we will look in detail at the NodePort service:
1. targetPort
2. port
3. clusterIp of the service
4. NodePort (external access)

node ports are usually between the range of 30000-32767. we have to provide the targetPort, if we don't specify, the port will be the same as the targetPort, and the nodePort will be assigned.

to assign the service to a certain pod, we use the *selector* to match the pods from the deployment/replicaset/pod definition file.

service-definition.yaml
```yaml
apiVersion: v1
kind: Service
metadata:
  name:  myapp-service
spec:
  type: NodePort
  ports:
  - targetPort: 80
    port: 80
    nodePort: 30008
  selector:
    app: myapp
    type: front-end
```

ClusterIP:

when we want communication between different pods. like between backend and frontend, and between the backend and the database.

service-definition-cluster.yaml
```yaml
apiVersion: v1
kind: Service
metadata:
  name:  back-end
spec:
  type: clusterIP
  ports:
  - targetPort: 80
    port: 80
  selector:
    app: myapp
    type: back-end
```


Load Balancer:

another type of service, but this time using a load balancing. gives us a single url for the end user, and integrates with the cloud provider to use whatever load balancer it has. if there ins't a load balancer, it will be just like NodePort type

service-definition-load-balancer.yaml
```yaml
apiVersion: v1
kind: Service
metadata:
  name:  myapp-service
spec:
  type: LoadBalancer
  ports:
  - targetPort: 80
    port: 80
  selector:
    app: myapp
    type: front-end
```
#### Lab - Services

```sh
kubectl get services -A
kubectl describe service kubernetes

kubectl get deployments -A
kubectl describe deployment/simple-webapp-deployment
#kubectl describe deployment/simple-webapp-deployment | grep -i image

vi service-definition-1.yaml
kubectl apply -f service-definition-1.yaml 
```
</details>

## Microservices Architecture

<!-- <details> -->
<summary>
Understanding microservices architecture
</summary>

we will use the Docker voting app, it has an interface to vote, to show the results, backend and database.

voting app -> in-memory DB (redis) -> worker -> db (postgresSQL) -> results app


running these commands simply don't work. docker-compose would be better
```sh
docker container run -d --rm --name=redis redis
docker container run -d --rm --name=db postgres:9.4
docker container run -d --rm --name=vote -p 5000:80 voting-app
docker container run -d --rm --name=result -p 5001:80 results-app
docker container run -d --rm --name=worker worker
```
we need to link them together with the *--link* flag. we match the host and the service name.

```sh
docker container run -d --rm --name=redis redis
docker container run -d --rm --name=db postgres:9.4
docker container run -d --rm --name=vote --link redis:redis -p 5000:80 voting-app
docker container run -d --rm --name=result --link db:db -p 5001:80 results-app
docker container run -d --rm --name=worker --link db:db --link redis:redis worker
```

but this is tedious. lets be smarter.

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

