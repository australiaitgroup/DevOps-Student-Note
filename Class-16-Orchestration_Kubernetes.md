# Lesson-16-Orchestration_Kubernetes


# What is Kubernetes (K8s) ?
Kubernetes is a portable, extensible, open-source platform for managing containerized workloads and services, that
facilitates both declarative configuration and automation. It has a large, rapidly growing ecosystem. Kubernetes 
services, support, and tools are widely available.


# History
![Alt text](images/container_evolution.svg?raw=true)
* __Traditional deployment era__: Early on, organizations ran applications on physical servers. There was no way to 
  define resource boundaries for applications in a physical server, and this caused resource allocation issues.

* __Virtualized deployment era__: As a solution, virtualization was introduced. It allows you to run multiple Virtual 
  Machines (VMs) on a single physical server's CPU. Virtualization allows applications to be isolated between VMs 
  and provides a level of security as the information of one application cannot be freely accessed by another
  application.
  
* __Container deployment era__: Containers are similar to VMs, but they have relaxed isolation properties to share the 
  Operating System (OS) among the applications. Therefore, containers are considered lightweight.

# Why you need Kubernetes and what it can do？
Containers are a good way to bundle and run your applications. In a __production__ environment, you need to manage the
containers that run the applications and ensure that there is __no downtime__. For example, if a container goes down, 
another container needs to start. Wouldn't it be easier if this behavior was handled by a system?

Kubernetes provides you with a framework to run distributed systems __resiliently__.

Kubernetes provides:
* Service discovery and load balancing
  * Kubernetes can expose a container using the DNS name or using their own IP address. If traffic to a container is
    high, Kubernetes is able to load balance and distribute the network traffic so that the deployment is stable.
* Storage orchestration 
  * Kubernetes allows you to automatically mount a storage system of your choice, such as local storages, 
    public cloud providers, and more.
* Automated rollouts and rollbacks 
  * You can describe the desired state for your deployed containers using Kubernetes, and it can change the actual 
    state to the desired state at a controlled rate. For example, you can automate Kubernetes to create new containers
    for your deployment, remove existing containers and adopt all their resources to the new container.
* Automatic bin packing
  * You provide Kubernetes with a cluster of nodes that it can use to run containerized tasks. You tell Kubernetes how
    much CPU and memory (RAM) each container needs. Kubernetes can fit containers onto your nodes to make the best use
    of your resources.
* Self-healing
  * Kubernetes restarts containers that fail, replaces containers, kills containers that don't respond to your 
    user-defined health check, and doesn't advertise them to clients until they are ready to serve.
* Secret and configuration management
  * Kubernetes lets you store and manage sensitive information, such as passwords, OAuth tokens, and SSH keys. 
    You can deploy and update secrets and application configuration without rebuilding your container images, and
    without exposing secrets in your stack configuration.
    
# Know the components

## Pod
A Pod is a Kubernetes abstraction that represents a group of one or more application containers (such as Docker), 
and some shared resources for those containers.

Those resources include:

* Shared storage, as Volumes
* Networking, as a unique cluster IP address
* Information about how to run each container, such as the container image version or specific ports to use

A Pod models an application-specific "logical host" and can contain different application containers which are 
relatively tightly coupled.

![Alt text](images/module_03_pods.svg?raw=true)

## Node 
A Pod always runs on a Node. A Node is a worker machine in Kubernetes and may be either a virtual or a physical machine,
depending on the cluster.

Each Node is managed by the control plane. A Node can have multiple pods, and the Kubernetes control plane automatically
handles scheduling the pods across the Nodes in the cluster. The control plane's automatic scheduling takes into account
the available resources on each Node.

Every Kubernetes Node runs at least:

* Kubelet, a process responsible for communication between the Kubernetes control plane and the Node; it manages the
  Pods and the containers running on a machine. 
* A container runtime (like Docker) responsible for pulling the container image from a registry, unpacking the 
  container, and running the application.

![Alt text](images/module_03_nodes.svg?raw=true)

### kubelet
An agent that runs on each node in the cluster. It makes sure that containers are running in a Pod.

### kube-proxy
kube-proxy is a network proxy that runs on each node in your cluster. kube-proxy maintains network rules on nodes.

### Container runtime
The container runtime is the software that is responsible for running containers.
Kubernetes supports several container runtimes: Docker, containerd, CRI-O, and any implementation of the Kubernetes
CRI (Container Runtime Interface).



## Cluster
A Kubernetes cluster consists of a set of worker machines, called nodes, that run containerized applications. 
Every cluster has at least one worker node.

The worker node(s) host the Pods that are the components of the application workload.

The control plane manages the worker nodes and the Pods in the cluster. 
* In production environments, the control plane usually 
  * runs across multiple computers and a cluster
  * usually runs multiple nodes
  
  providing fault-tolerance and high availability.

## Control Plane
![Alt text](images/components-of-kubernetes.svg?raw=true)

The control plane's components make global decisions about the cluster (for example, scheduling), as well as detecting
and responding to cluster events (for example, starting up a new pod when a deployment's replicas field is unsatisfied).

Control plane components can be run on any machine in the cluster. However, for simplicity, set up scripts typically 
start all control plane components on the same machine, and do not run user containers on this machine.

### kube-apiserver
The API server is a component of the Kubernetes control plane that exposes the Kubernetes API. The API server is the
front end for the Kubernetes control plane.

### etcd
Consistent and highly-available key value store used as Kubernetes' backing store for all cluster data.

Ref: https://etcd.io/docs/v3.5/demo/

### kube-scheduler
Control plane component that watches for newly created Pods with no assigned node, and selects a node for them to run
on.

### kube-controller-manager
Control plane component that runs controller processes.


### cloud-controller-manager
A Kubernetes control plane component that embeds cloud-specific control logic. The cloud controller manager lets you
link your cluster into your cloud provider's API, and separates out the components that interact with that cloud 
platform from components that only interact with your cluster.

The cloud-controller-manager only runs controllers that are specific to your cloud provider. If you are running
Kubernetes on your own premises, or in a learning environment inside your own PC, the cluster does not have a cloud
controller manager.


## Deployment

Once you have a running Kubernetes cluster, you can deploy your containerized applications on top of it. 
To do so, you create a Kubernetes Deployment configuration. The Deployment instructs Kubernetes how to create
and update instances of your application. Once you've created a Deployment, the Kubernetes control plane schedules the
application instances included in that Deployment to run on individual Nodes in the cluster.




Note: be careful with the price of Kubernete clusters, use price calculator to do an estimate


# Swarm
Current versions of Docker include swarm mode for natively managing a cluster of Docker Engines called a swarm. 
Use the Docker CLI to create a swarm, deploy application services to a swarm, and manage swarm behavior.







# Differences?
![Alt text](images/DIFF.PNG?raw=true)
Ref: https://www.youtube.com/watch?v=9_s3h_GVzZc

# Further Reading
https://www.ibm.com/cloud/blog/docker-swarm-vs-kubernetes-a-comparison

# Swarm
https://docs.docker.com/engine/swarm/
https://docs.docker.com/engine/swarm/key-concepts/#nodes
https://docs.docker.com/engine/swarm/key-concepts/#services-and-tasks
https://docs.docker.com/engine/swarm/key-concepts/#load-balancing


#K8s
https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
https://kubernetes.io/docs/concepts/architecture/nodes/
https://kubernetes.io/docs/concepts/workloads/pods/
https://kubernetes.io/blog/2016/07/autoscaling-in-kubernetes/#benefits-of-autoscaling


# Description

This is to demostrate Docker Swarm's usage.

# Steps

## 1. Open terminal and change to `docker-swarm` folder
```
cd docker-swarm
```
## 2. Run run.sh script
```
./run.sh
```
or
```
./run-mac.sh
```
## 3. Open visualizer: http://127.0.0.1:8080
![Alt text](images/swarm.png?raw=true)


## 4. Kill a container and see if it will be recreated.
- Find a container
```
docker ps
```
- Kill a container
```
docker kill your_container_id
```
## 5. Change scaling to spin up more containers.
- Find your service
```
docker service ls
```
- View your service
```
docker service ps your_service_id
```
- Change sclaing of your service
```
docker service scale your_service_id=25
```


# What is minikube?
Minikube is local Kubernetes, focusing on making it easy to learn and develop for Kubernetes.

All you need is Docker (or similarly compatible) container or a Virtual Machine environment, and Kubernetes is a single command away: `minikube start`

# Prerequisite
* 2 CPUs or more
* 2GB of free memory
* 20GB of free disk space
* Internet connection
* Container or virtual machine manager, such as: Docker, QEMU, Hyperkit, Hyper-V, KVM, Parallels, Podman, VirtualBox, or VMware Fusion/Workstation

# Installation
https://minikube.sigs.k8s.io/docs/start/

# Goal of this exercise
* Understand cluster, node, deployment concepts
* Use Kubectl to interact with the cluster

# Simple operations

## Step 1. Start your local cluster
`minikube start`

## Step 2. Start a cluster dashboard
`minikube dashboard`

## Step 3. Create a deployment
`kubectl create deployment hello-node --image=registry.k8s.io/e2e-test-images/agnhost:2.39 -- /agnhost netexec --http-port=8080`

## Step 4. View the Deployment
`kubectl get deployments`

``` 
NAME         READY   UP-TO-DATE   AVAILABLE   AGE
hello-node   1/1     1            1           1m
```

## Step 5. View the Pod
`kubectl get pods`
```
NAME                          READY     STATUS    RESTARTS   AGE
hello-node-5f76cf6ccf-br9b5   1/1       Running   0 
```

## Step 6 View cluster events
`kubectl get events`
```
LAST SEEN   TYPE     REASON                    OBJECT                             MESSAGE
83s         Normal   Scheduled                 pod/hello-node-7579565d66-tzzk6    Successfully assigned default/hello-node-7579565d66-tzzk6 to minikube
80s         Normal   Pulling                   pod/hello-node-7579565d66-tzzk6    Pulling image "registry.k8s.io/e2e-test-images/agnhost:2.39"
56s         Normal   Pulled                    pod/hello-node-7579565d66-tzzk6    Successfully pulled image "registry.k8s.io/e2e-test-images/agnhost:2.39" in 24.760728351s (24.760737275s including waiting)
55s         Normal   Created                   pod/hello-node-7579565d66-tzzk6    Created container agnhost
55s         Normal   Started                   pod/hello-node-7579565d66-tzzk6    Started container agnhost
83s         Normal   SuccessfulCreate          replicaset/hello-node-7579565d66   Created pod: hello-node-7579565d66-tzzk6
83s         Normal   ScalingReplicaSet         deployment/hello-node              Scaled up replica set hello-node-7579565d66 to 1
7m36s       Normal   Starting                  node/minikube                      Starting kubelet.
7m33s       Normal   NodeHasSufficientMemory   node/minikube                      Node minikube status is now: NodeHasSufficientMemory
7m33s       Normal   NodeHasNoDiskPressure     node/minikube                      Node minikube status is now: NodeHasNoDiskPressure
7m33s       Normal   NodeHasSufficientPID      node/minikube                      Node minikube status is now: NodeHasSufficientPID
7m35s       Normal   NodeAllocatableEnforced   node/minikube                      Updated Node Allocatable limit across pods
7m26s       Normal   Starting                  node/minikube                      Starting kubelet.
7m26s       Normal   NodeHasSufficientMemory   node/minikube                      Node minikube status is now: NodeHasSufficientMemory
7m26s       Normal   NodeHasNoDiskPressure     node/minikube                      Node minikube status is now: NodeHasNoDiskPressure
7m26s       Normal   NodeHasSufficientPID      node/minikube                      Node minikube status is now: NodeHasSufficientPID
7m26s       Normal   NodeAllocatableEnforced   node/minikube                      Updated Node Allocatable limit across pods
7m26s       Normal   NodeNotReady              node/minikube                      Node minikube status is now: NodeNotReady
7m16s       Normal   NodeReady                 node/minikube                      Node minikube status is now: NodeReady
7m13s       Normal   RegisteredNode            node/minikube                      Node minikube event: Registered Node minikube in Controller
7m12s       Normal   Starting                  node/minikube
```
## Step 7. View config:
`kubectl config view`

## Step 8. View logs:
`kubectl logs hello-node-<blah>`

## Step 9. Make the node accessible outside kubernetes
`kubectl expose deployment hello-node --type=LoadBalancer --port=8080`

The `--type=LoadBalancer` flag indicates that you want to expose your Service outside of the cluster.

The application code inside the test image only listens on TCP port 8080. If you used kubectl expose to expose a different port, clients could not connect to that other port.

## Step 10. View the Service:
`kubectl get services`

## Deployment vs Service
### Deployment:
Purpose: A Deployment is primarily responsible for maintaining a specified number of pod replicas running. It provides declarative updates for Pods and ReplicaSets.

####  Components:

Pods: The smallest deployable units in Kubernetes that can be created and managed. They host your application containers.

ReplicaSets: Ensures that a specified number of pod replicas are maintained. If a Pod fails, the ReplicaSet creates a new one to replace it.
Features:

Rolling Updates: Deployments allow you to update the Pod template and roll out updates gradually, ensuring zero downtime.

Scaling: You can scale the number of Pods in a Deployment up or down.

Revisions & Rollbacks: Maintains a revision history allowing you to roll back to a previous version if needed.
Pod Health: Replaces Pods that become unhealthy or unresponsive.

Use Case: When you want to deploy a stateless application, manage its desired state, and easily update or rollback when necessary.

### Service:
Purpose: A Service in Kubernetes is an abstraction that defines a set of Pods and a policy to access them. Its primary purpose is to provide a stable IP address, DNS name, and port through which the Pods can be accessed, even as they are scaled or replaced.

####  Types of Services:

ClusterIP: Internal-only IP. Default service type and is only reachable within the cluster.

NodePort: Exposes the service on a static port on each Node’s IP.

LoadBalancer: Exposes the service externally using a cloud provider's load balancer.

ExternalName: Maps a service to a DNS name.
Features:

Stable Endpoint: Provides a consistent IP address and DNS name, even if underlying Pods are replaced.

Load Distribution: Distributes network traffic to all Pods matching the service's selector.

Service Discovery: Kubernetes supports two primary modes of finding a Service - environment variables and DNS.

Use Case: When you need a consistent way to access a set of Pods or when you want to expose an application externally.

## Step 11. Run the app in your browser
`minikube service hello-node`

## Step 12. Check addon list

`minikube addons list`

## Step 13. Cleanup
```
kubectl delete service hello-node
kubectl delete deployment hello-node
```


# Nodeport
## Step 1. create a deployment and expose it
```
kubectl create deployment hello-minikube --image=kicbase/echo-server:1.0

kubectl expose deployment hello-minikube --type=NodePort --port=8080
```

## Step 2. check the status and port forward
check if the service is running

`kubectl get services hello-minikube`

use kubectl to forward the port

`kubectl port-forward service/hello-minikube 7080:8080`

Your application is now available at http://localhost:7080/.

## Step 3. delete
`kubectl delete service hello-minikube`

`kubectl delete deployment hello-minikube`

# LoadBalancer


## Step 1. Create a deployment and expose it
```
kubectl create deployment balanced --image=kicbase/echo-server:1.0

kubectl expose deployment balanced --type=LoadBalancer --port=8080
```
## Step 2. Open a Tunnel
start the tunnel to create a routable IP for the ‘balanced’ deployment

`minikube tunnel`


Open `http://localhost:8080/`

## Step 3. Scale up
`kubectl scale --replicas=3 deployment/balanced`

`kubectl get deployments`

Now refresh the link a few times and see if the load balancer is taking effect

## Step 4. Cleanup
`kubectl delete service balanced`
`kubectl delete deployment balanced`


# Ingress

## Step 1. Enable Ingress Addon
`minikube addons enable ingress`

## Step 2. Apply the contents
`kubectl apply -f https://storage.googleapis.com/minikube-site-examples/ingress-example.yaml`


## Step 3. check address available
`kubectl get ingress` 
```
NAME              CLASS   HOSTS   ADDRESS          PORTS   AGE
example-ingress   nginx   *       <your_ip_here>   80      5m45s
```

## Step 4. Open a Tunnel
`minikube tunnel`

## Step 5. Open another tab and hit the endpoint
`curl 127.0.0.1/foo`
```
Request served by foo-app
...
```

`curl 127.0.0.1/bar`

```
Request served by bar-app
...
```


## Step 6. Cleanup
```
kubectl delete -f https://storage.googleapis.com/minikube-site-examples/ingress-example.yaml
```



# Deploy our first demoapp via yaml file
## Step 1. Understand demoapp.yaml
Open `minikube/config/demoapp.yaml`

```
apiVersion: apps/v1
```
apiVersion: This defines the version of the Kubernetes API you're using to create this object. For Deployments, the version is typically apps/v1.

```
kind: Deployment
```
kind: This specifies the type of resource you want to manage, in this case, a Deployment. Deployments are high-level objects that allow for declarative updates to Pods and ReplicaSets.


```
metadata:
  name: demoapp
  namespace: jiangren
```

metadata: Information to uniquely identify the Deployment.

name: The name assigned to this Deployment.

namespace: The Kubernetes namespace where this Deployment is created. Here, it's jiangren.

```
spec:
  replicas: 12
```
spec: Specifies the desired characteristics of the Deployment.
replicas: The number of pod replicas you want to maintain. Here, it's 12.

```  
selector:
    matchLabels:
      app: demoapp
```
selector: Dictates how the Deployment recognizes the pods it should manage.

matchLabels: A map of {key, value} pairs. A pod must meet all label conditions to be considered a match. Here, it selects pods with the label `app=demoapp`.


```
  template:
    metadata:
      labels:
        app: demoapp
```

template: Defines the blueprint for the pods.

* metadata.labels: The labels to assign to each pod. This is crucial because the Deployment's selector will look for this label.


```
    spec:
      containers:
        - name: echo
          image: stevesloka/echo-server
          command: ["echo-server"]
          args:
            - --echotext=This is the jiangren test site!
          imagePullPolicy: IfNotPresent
          ports:
            - name: http
              containerPort: 8080
```

spec: Details of the container to be created in each pod.

containers: List of containers to be run within the pod.

name: Name of the container.

image: Docker image to be used.

command: Command run once the container starts. Overrides the 
default command provided by the Docker image.

args: Arguments passed to the command.

imagePullPolicy: Determines when to pull the image. IfNotPresent means it'll pull the image only if it doesn't exist locally.

ports: Specifies the port information of the container.

name: Name of the port (for reference purposes).
containerPort: The port on which the container will listen.

```
kind: Service
```
kind: Specifies that you're defining a Service, which is a construct to expose applications running on a set of pods.

```
  type: LoadBalancer
```
type: Specifies the type of service. LoadBalancer means it'll try to get an external IP to expose the Service.

## Step 2. Apply the contents
This will create the service and deploy your app
```
cd minikube/config
kubectl apply -f namespace.yaml
kubectl apply -f demoapp.yaml
```

## Step 3. Expose the service
```
minikube service demoapp --namespace=jiangren
```
or 

```
minikube tunnel
```


# CronJob

## Step 1. run the demo job
```
kubectl apply -f demojob.yaml
```

## Step 2. check the pods status and the logs
```
kubectl get pods -n jiangren
kubectl logs hello-28283126-x5tzg -n jiangren
```

## Step 3. read the demojob.yaml


## Step 4. check the cheatsheet to interact with pods
https://kubernetes.io/docs/reference/kubectl/cheatsheet/



# Roll out a change to demoapp
## Step 1. Have a CI/CD to push your latest build to a registry
The push to registry will not be demoed in this note.

Here is how you can check the image version:
```
kubectl describe deployment demoapp -n jiangren | grep Image:
```


## Step 2. Set an image or update your yaml file

Method 1. change the yaml file and apply
```
...
spec:
  containers:
    - name: echo
      image: stevesloka/echo-server:NEW_VERSION
...
```

```
kubectl apply -f demoapp.yml
```

Method 2. simply use kubectl
``` 
kubectl set image deployment/demoapp echo=stevesloka/echo-server:NEW_VERSION -n jiangren
```
## Step 3. Monitor the rollout

```
kubectl rollout status deployment/demoapp -n jiangren
```

## Step 4. Rollback If Needed:
```
kubectl rollout undo deployment/demoapp -n jiangren
```

## Step 5. Scaling and Managing:
As you've specified a large number of replicas (12), Kubernetes will ensure a rolling update with zero downtime. 

It will gradually terminate old pods and create new ones with the updated image.

The specifics of how many are terminated/created at once can be managed with strategy parameters in the Deployment, but that's an advanced topic. https://kubernetes.io/docs/concepts/workloads/controllers/deployment/

# ConfigMap
Many applications rely on configuration which is used during either application initialization or runtime. Most times, there is a requirement to adjust values assigned to configuration parameters. ConfigMaps are a Kubernetes mechanism that let you inject configuration data into application pods.

The ConfigMap concept allow you to decouple configuration artifacts from image content to keep containerized applications portable. For example, you can download and run the same container image to spin up containers for the purposes of local development, system test, or running a live end-user workload.

## Basics
### Step 1. Create a config

Create a key value pair from command line. 
```
kubectl create configmap special-config --from-literal=special.how=very
```
You can also create key value pair from yaml file, env file, and any other local files.

### Step 2. Create the pod and check
Read the following file to understand how configMap is configured
```
kubectl create -f https://kubernetes.io/examples/pods/pod-single-configmap-env-variable.yaml
```

Run this command to check the injected configuration
```
kubectl logs dapi-test-pod
```

### Step 3. Clean up
```
kubectl delete configmap special-config
kubectl delete pod dapi-test-pod
```

## Config a Redis

### Step 1. Create the configMap
```
cd minikube/config
kubectl apply -f example-redis-config.yaml
```
check if the configuration is there
```
kubectl describe configmap/example-redis-config
```

### Step 2. Create a Redis pod
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/website/main/content/en/examples/pods/config/redis-pod.yaml
```
and then check the values
```
kubectl exec -it redis -- redis-cli
```
Check maxmemory:
```
127.0.0.1:6379> CONFIG GET maxmemory
```
Check maxmemory-policy
```
127.0.0.1:6379> CONFIG GET maxmemory-policy
```


# Description

This is to show how we can create clusters, deployment and tasks in GKE.

After that, it shows a way to create application via market place.

# Tasks
## Task #1: Create a cluster and deploy a sample application to it

### Step 1: Create a new project
https://console.cloud.google.com/projectcreate

![Alt text](images/GKE1.png?raw=true)

### Step 2: Open GKE Cluster dashboard
![Alt text](images/GKE2.png?raw=true)

### Step 3: (First time user) You may need to fill in your credit card info to proceed
![Alt text](images/Enable_k8s.png?raw=true)
When click on ENABLE -> You will see the following pop up. Please fill in your billing details and come back.
Google provides $300 credits for the first time user.
![Alt text](images/billing_required.png?raw=true)

### Step 4: Otherwise, you should see Create Cluster
![Alt text](images/GKE_create_cluster.png?raw=true)
click on CREATE and choose Standard to configure
![Alt text](images/GKE_choose_standard.png?raw=true)

### Step 4: Configure the cluster. You can choose AU zone to have lower latency to the cluster.
We will use static version for now. You can also try a Release Channel to keep your cluster version up-to-date.
![Alt text](images/GKE_configure_cluster.png?raw=true)

### Step 5: After the cluster is ready, click deploy to deploy an application.
![Alt text](images/GKE5.png?raw=true)

### Step 6: Deploy the Easy CRM: davisjiangren01/easycrm:latest 
Note: You can use your own image. For deployment with DB, please refer to `5.GKE2.md`.
![Alt text](images/GKE6.png?raw=true)

### Step 7: Continue to deploy 
![Alt text](images/GKE7.png?raw=true)

### Step 8: When the pods are deployed. You can check the pods in "Workloads" tab. Please click "Expose" to continue create a service.
![Alt text](images/GKE8.png?raw=true)

### Step 9: Use default 8090 port and expose. 
![Alt text](images/GKE9.png?raw=true)

### Step 10: When the service is ready, you will get external endpoints.  
![Alt text](images/GKE10.png?raw=true)

### Step 11: Access the public IP address in web browser.  
![Alt text](images/GKE11.png?raw=true)

## Task 2: Create application from Marketplace
### Step 1: Search "wordpress" or sth else in the market place.
![Alt text](images/WP01.png?raw=true)

### Step 2: Click configure to continue
![Alt text](images/WP02.png?raw=true)

### Step 3: Input configuration and then click "Deploy"
![Alt text](images/WP03.png?raw=true)

### Step 4: Wordpress takes a bit time to get deployed
![Alt text](images/WP04.png?raw=true)

### Step 5: You can find credential in the detail page when it's ready.
![Alt text](images/WP05.png?raw=true)

### Step 6: A sample credential is as below.
![Alt text](images/WP06.png?raw=true)

### Step 7: You can find default "Hello world" page from wordpress.
![Alt text](images/WP08.png?raw=true)

### Step 8: Login with the credential in "Step 5"
![Alt text](images/WP09.png?raw=true)

### Step 9: You should login to the page successfully.
![Alt text](images/WP11.png?raw=true)

### Step 10: You can change theme in "Appearance" tab.
![Alt text](images/WP13.png?raw=true)

## Task 3: Run kubectl commands in the cluster console

Go to cluster tab and open a console. Try kubectl commands in our slides.

# Description
This is to show how we can apply a free sub domain name and bind to our own website.

# Step 1: Apply a free account
Open http://freedns.afraid.org/signup/?plan=starter and apply.
![Alt text](images/DOMAIN01.png?raw=true)

# Step 2: Register a domain name. I suggest to use an org domain as it's faster to resolve and update.
http://freedns.afraid.org/domain/registry/
![Alt text](images/DOMAIN02.png?raw=true)

# Step 3: Choose "A" type, put whatever you like in the "Subdomain" field and set destination as the IP address of your website. In this case, use your Wordpress website.
http://freedns.afraid.org/subdomain/
![Alt text](images/DOMAIN03.png?raw=true)

# Step 4: You should see domain name is ready.
![Alt text](images/DOMAIN04.png?raw=true)

# Step 5: You should be able to view your website via public DNS in a few seconds.
![Alt text](images/DOMAIN05.png?raw=true)

# Step 6: Give the domain name to your team member to see if they can access successfully.


# Description

This is to show how we can deploy an application deployed by ourselves.

# Tasks
## Task #1: Build a docker image and push it to docker hub.
See: https://github.com/JiangRenDevOps/DevOpsLectureNotes/blob/master/WK4-Virtualization-Containerization-and-orchestration/4.docker-registry.md


## Task #2: Create a DB service (Not proper way. Ideally we should use managed database)
### Step 1: Click deploy to deploy an application.
![Alt text](images/GKE5.png?raw=true)

### Step 2: Deploy "mongo" application
![Alt text](images/CMS01.png?raw=true)

### Step 3: Continue to deploy
![Alt text](images/CMS02.png?raw=true)

### Step 4: Change Autoscaler to limit DB instance to one master node
![Alt text](images/CMS05.png?raw=true)

### Step 5: Set Maximum to 1
![Alt text](images/CMS06.png?raw=true)

### Step 6: Please click "Expose" to continue create a service.
![Alt text](images/CMS07.png?raw=true)

### Step 7: Use default 27017 port for mongodb and expose.
![Alt text](images/CMS08.png?raw=true)

### Step 8: When the service is ready, you will get external endpoints. Please choose "Cluster IP" as service type as we don't want to expose the service to public internet. The DB should only be used by an app.
![Alt text](images/CMS09.png?raw=true)


## Task #4: Create an application service 
### Step 1: Click deploy to deploy an application.
![Alt text](images/GKE5.png?raw=true)

### Step 2: Use your image in docker hub. Here I use "davisliu/todoapp" which is a sample todolist app.
![Alt text](images/CMS10.png?raw=true)

### Step 3: Create a deployment as usual.
![Alt text](images/CMS11.png?raw=true)
![Alt text](images/CMS12.png?raw=true)

### Step 4: Expose the deployment as usual. You can refer to the below link.
Please use `8080` as target port if the webserver in your image uses port `8080`.
https://github.com/JiangRenDevOps/DevOpsLectureNotes/blob/master/WK5-Kubernetes-GKE-CMS/2.GKE1.md

### Step 5: You should be able to see the below app if you use my sample image. Or your website if you use your own image.
The application should be working. You can add new TODO item and remove TODO item.
![Alt text](images/CMS13.png?raw=true)

### Step 6: Add a domain name for the website.


# Description

This is to show how to delete GKE project.

It should be fine to keep the cluster in the account during the training period. You can remove the project to practice from scratch.

# Step 1: Click triple dots on the right side of the portal and select "Project setting"
![Alt text](images/CLEANUP01.png?raw=true)

# Step 2: Click "SHUT DOWN"
![Alt text](images/CLEANUP02.png?raw=true)

# Step 3: Input the project ID and click "SHUT DOWN".
![Alt text](images/CLEANUP03.png?raw=true)

# Docker and Orchestration related

* Task 1. Dockerise any web project (e.g. EasyCRM, P3) and push the image to dockerhub 
* Task 2. Use docker-compose to spin up a nginx and the webapp containers. 
* Task 3. Could you use docker swarm to make a few replicas of the webapp and balance the load among them?   
* ( Optional ) If you use EasyCRM, What problem do you see with Task 3? (Hint: stateful vs stateless server)

* Stretch Task: Deploy your app to Kubernetes
* Question 1: Are you able to scale up and down you webapp?
* Question 2: Are you able to roll out a new version (e.g. different tag) of your webapp? What are the steps?
Stretch Question: Are you able to configure the nginx as a reverse proxy and load balancer for the webapp?

   (Hints:
   * I. You may need to pick up the nginx knowledge and rewrite the nginx config file to suit the cluster
   * II. How to configure so that your nginx can detect and load balance requests to any new pods?)




# Resume
1. What do you learn from https://enhancv.com/resume-examples/famous/sheryl-sandberg/ Sheryl Sandberg's Resume?
List a few points that you can introduce to improve your resume.
   
2. Start rewriting your resume and let others to review when you believe that you made progress.
   ( Hint: Use Grammarly to fix grammer mistakes; Use bullet points; Think about your achievements not daily tasks)    
