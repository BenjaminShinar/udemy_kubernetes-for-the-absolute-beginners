<!--
ignore these words in spell check for this file
// cSpell:ignore udemy Microservices Kubeadm replicaset replicasets Mesos kubectl katakoda systeminfo virtualbox vmwarefusion hyperv vmware podman kops kube gcloud Vagrantile kubemaster kubenode netfilter nodeprobe keyrings
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
    - [Google Kubernetes Engine (GKE)](#google-kubernetes-engine-gke)
    - [Amazon Elastic Kubernetes Service (EKS)](#amazon-elastic-kubernetes-service-eks)
    - [Azure Kubernetes Service (AKS)](#azure-kubernetes-service-aks)
  - [Setup Multi Node Cluster Using Kubeadm](#setup-multi-node-cluster-using-kubeadm)



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

<details>
<summary>
Understanding microservices architecture and creating a multi-pod application.
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


> our goals:
> 1. deploy containers on kubernetes cluster
> 2. enable connectivity between containers
> 3. enable external access

we don't have containers directly in kubernetes, we have pods. we will start with deploying pods and then move up to deployments. for connectivity, we need to think about who talks to who:
- external -> voting app - redis
- worker app -> redis
- worker app -> postgres
- external -> results app -> postgres

the worker app isn't being accessed by any other app or user.

app| name | port/targetPost | nodePort | image
----|---|-----|---|----
voting app |voting-app-pod | 80 | 30004|[kodekloud/examplevotingapp_vote:v1](https://hub.docker.com/r/kodekloud/examplevotingapp_vote)
result app | result-app-pod| 80| 300005|[kodekloud/examplevotingapp_result:v1](https://hub.docker.com/r/kodekloud/examplevotingapp_result)
redis | redis|6379|NA|[redis](https://hub.docker.com/_/redis)
postgres | db| 5432 | NA|[postgressql](https://hub.docker.com/_/postgres)
worker | worker-pod | NA |NA | [kodekloud/examplevotingapp_worker:v1](https://hub.docker.com/r/kodekloud/examplevotingapp_worker)

to make a component accessed by another, we can't use ip address because if the pod restarts, the address will change,that's why we use services.

we need to name the services properly, because the source code requires the name, in a real world environment, we would use environment variables
1. redis (redis, ClusterIP)
2. db (postgres, ClusterIP, required username + password)
3. voting-app (voting app, NodePort)
4. results-app (results app, NodePort)

we start with creating pod definition files.

- voting_app_pod.yaml
- result_app_pod.yaml
- redis_pod.yaml
- postgres_pod.yaml
- worker_app_pod.yaml
- redis_service.yaml
- postgres_service.yaml
- voting_app_service.yaml
- results_app_service.yaml
  
for the postgres pod, we need a user name and password, we could use secrets, or a vault, but that's for another time, we use hardcoded environment variables instead. as an intermediate step, we could use an environment resource instead.

now that we created the files, we can start creating them on the the cluster.

we assume we have a cluster, like minikube or aws, we might need to call `minikube service` to expose the app

```sh
cd voting_app_demo
kubectl create -f voting_app_pod.yaml -f voting_app_service.yaml
kubectl get pod, service
#minikube service voting-service --url

kubectl create -f redis_pod.yaml -f redis_service.yaml
kubectl create -f postgres_pod.yaml -f postgres_service.yaml
kubectl create -f worker_app_pod.yaml
kubectl get pod, service

kubectl create -f result_app_pod.yaml -f result_app_service.yaml
kubectl get pod, service
#minikube service result-service --url
```

deploying pods doesn't help us scale the app, and we can't update the pods without taking them down. so now we will try using a deployment instead, that we can add more replicas.

- voting_app_deployment.yaml
- redis_deployment.yaml
- postgres_deployment.yaml
- worker_app_deployment.yaml
- results_app_deployment.yaml

so before we use the deployments, lets remove all the previous pods and service
```sh
cd voting_app_demo
kubectl get pods
kubectl delete -f voting_app_pod.yaml -f result_app_pod.yaml -f worker_app_pod.yaml -f postgres_pod.yaml -f redis_pod.yaml
kubectl get pods
kubectl create -f postgres_deployment.yaml -f redis_deployment.yaml -f worker_app_deployment.yaml -f result_app_deployment.yaml -f voting_app_deployment.yaml

kubectl get deployment, svc
#minikube service result-service.yaml --url
#minikube service voting-service.yaml --url

kubectl scale deployment voting-app-deploy --replicas=3
kubectl get deployments
```

</details>

## Kubernetes on Cloud

<details>
<summary>
Getting started with kubernetes on popular cloud services.
</summary>

we can host kubernetes on various platforms, we can divide them into two categories:

> - SelfHosted / Turnkey Solution
>   - you can provision VMs
>   - you configure VMs
>   - you use scripts to deploy the cluster
>   - you maintain the VMs yourself
>   - e.g., kubernetes on AWS using kops or KubeOne
> - Hosted Solution (managed solutions)
>   - kubernetes as a service
>   - provider provisions VMs
>   - provider installs kubernetes
>   - provider maintains VMs
>   - e.g., google container engine (GKE)

### Google Kubernetes Engine (GKE)

need a google cloud account

<kbd>Compute</kbd> -> <kbd>Kubernetes Engine</kbd>\
<kbd>Create Cluster</kbd>\
clusterBasics:
 - change name
 - don't touch location or zone
 - either use static or dynamic kubernetes version
 - <kbd>node Pools</kbd>
   - stick with defaults

<kbd>Create</kbd>

connect to the cloud shell with the <kbd>connect</kbd> button and take a command for the *gcloud* app to get the credentials. this shell has kubectl installed so we just need to run the given command, and we are connected.

we get the files from the git repository. the files are a bit different because we use a LoadBalancer rather than a NodePort. w

```sh
git clone https://github.com/kodedloudhub/example-voting-app.git
cd example-voting-app/
ls
kubectl create -f voting-app-deploy.yaml -f voting-app-service.yaml
kubectl create -f redis-deploy.yaml -f redis-service.yaml
kubectl create -f postgres-deploy.yaml -f postgres-service.yaml
kubectl create -f worker-app-deploy.yaml
kubectl create -f result-app-deploy.yaml -f result-app-service.yaml

# create everything in the folder
# kubectl create -f .

kubectl get deployment, svc
```

in the google kubernetes dashboard we can see the <kbd>Services & Ingress</kbd> to get the ip addresses.

### Amazon Elastic Kubernetes Service (EKS)

for amazon, we need an AWS account, this is more complicated:

- EKS Cluster Role
- IAM Role for Node Group
- VPC
- EC2 Key Pair which can be used to SSH into the worker nodes
- AWS CLI and credentials

Step 1: create amazon eks cluster

  - configure cluster
    - cluster name
    - kubernetes version
    - cluster service role
  - specify networking
    - use default vpc
  - configure logging
    - nothing
  - review and create
    - nothing

Step 2: Add node groups

<kbd>Compute</kbd> tab, <kbd>Add node Group</kbd>
  - configure Node group
    - node group name
    - node IAM Role
    - default subnets
    - ssh key pair for ssh-ing into the worker nodes
  - set compute configuration
    - AMI type (virtual machine image)
    - Instance type
    - disk size
  - set scaling configuration
    - use defaults
  - review and create
    - nothing
    
<kbd>Create</kbd>

Step 3: configure kubectl

we need aws cli configured to work with our aws account in our terminal.
```sh
kubectl version
aws --version
aws eks --region <region> update-kubeconfig --name <cluster name>
kubectl config view
```
and now we can do the same as what we did in the google cloud option

```sh
git clone https://github.com/kodedloudhub/example-voting-app.git
cd example-voting-app/
ls
kubectl create -f voting-app-deploy.yaml -f voting-app-service.yaml
kubectl create -f redis-deploy.yaml -f redis-service.yaml
kubectl create -f postgres-deploy.yaml -f postgres-service.yaml
kubectl create -f worker-app-deploy.yaml
kubectl create -f result-app-deploy.yaml -f result-app-service.yaml

# create everything in the folder
# kubectl create -f .

kubectl get deployment, svc
```

then delete everything to stop paying amazon so much money.

### Azure Kubernetes Service (AKS)

we need a azure account. open AKS.

<kbd>Add</kbd> and select <kbd>Add Kubernetes cluster</kbd>
- Basics
  - subscription
  - resource group <kbd>create new</kbd>
  - kubernetes cluster name
  - region
  - kubernetes version
  - node size, node count
- Node pools
  - nothing
- Authentication
  - choose <kbd>Service principal</kbd>
- Networking
  - nothing
- Integrations
  - nothing
- Tags
  - nothing
- Review + create
  - nothing

<kbd>Create</kbd>

type the name of the resource in the searchbox, then use the cloud shell, we might be promoted to configure storage. we configure the kubectl to work with the cluster.

```sh
az aks get-credentials --resource-group <resource group name> --name <cluster name>
kubectl get nodes
```
and now we can clone the repository and create the deployments.


</details>

## Setup Multi Node Cluster Using Kubeadm

<details>
<summary>
Using Kubeadm to create a multi node cluster
</summary>

The kubeadm (kube admin) tool, which is used to set up a multi-node cluster.

we need multiple machines (can be virtual), we need to install a container runtime on each node, like docker, and installing the kubeadm tool. then we initialize the master node. we need a special networking configuration between the machine. the last step is to join the worker nodes (machines) to the master node.

the demo uses virtual box and a "Vagrantile" file which describes the nodes and network. we can see the file in the [github repository](https://github.com/kodekloudhub/certified-kubernetes-administrator-course)

we follow the instruction from the[installing kubeadm page](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)
```sh
vagrant status
vagrant up
vagrant status
vagrant ssh kubemaster
$ uptime
$ logout
vagrant ssh kubenode01
$ logout
vagrant ssh kubenode02
$ cat /etc/hostname
$ logout
```
now we have the nodes, and we need to configure the cluster.

we need to enable/load bridged traffic, we do this on all the nodes.
```sh
#check this
lsmod | grep br_netfilter
#if nothing, then run
sudo nodeprobe br_netfilter
# now we see something
lsmod | grep br_netfilter
```
we also need to setup kernel parameters
```sh
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
# then check
sudo sysctl --system
```

we install the container run time
```sh
#get privileges
sudo -i

apt-get update && apt-get install -y \
apt-transport-https ca-certificates curl software-properties-common gnupg2

# google public signing key
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg

#Add the Kubernetes apt repository
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

#install docker
#

#set up docker daemon
sudo mkdir /etc/docker
cat <<EOF | sudo tee /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF

# start docker service
sudo systemctl enable docker
sudo systemctl daemon-reload
sudo systemctl restart docker

#verify status
systemctl status docker.service

# installing kubeadm tools
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

if we use docker, we don't need to configure cgroup drivers.

now we move on to [configuring the cluster](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/)

we initialize the control-plane node, we can skip the first step if we have one master node. we then configure the pod network, we can use one of several options.


now we run this on the master node (not on the worker nodes)
```sh
#check ip, inet
ifconfig enp0s8

kubeadm init --pod-network-cider 10.244.0.0/16 --apiserver-advertise-address=192.168.56.2

```

now we switch back to the regular (not sudo root) user
```sh
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

now we use the command we got from the master (`kubeadm join`) inside the worker nodes to join them into the cluster. we can then check the status of the cluster
`kubectl get nodes`

we need to install the pod network, like weave
```sh
kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
kubectl get nodes
```

we can now start adding resources to the cluster

```sh
kubectl run nginx --image=nginx
kubectl get pods
```
</details>