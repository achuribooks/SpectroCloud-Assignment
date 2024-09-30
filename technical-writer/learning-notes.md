
# Important Terms and Concepts

## Docker
Docker is a program that bundles ("containers") an application and all its necessary dependencies. These **containers** exist in isolated environments and can be scaled and distributed as necessary. The application will always run the same no matter the medium it is on because the configurations are identical. Containers are more resource efficient than virtual machines and are great for microservices.

There are three main steps to assembling a container, often referred to as "Build, Ship, Run": 
- **Build** a **Dockerfile**
- **Ship** a Docker **image**
- **Run** and create a **container**

### Dockerfile
A Dockerfile is the recipe for a containerized application. The file is structured, beginning with an OS or language, and specifies all dependencies the application needs in order to run. Dependencies tend to be very detailed, often all the way down to a version number.

Dockerfiles are assembled in "layers"; the Dockerfile is read top-down and assembles the layers bottom-up using commands like `FROM`, `RUN`, and `COPY`. When you run a Dockerfile, you create an **image**.

### Image
An image is an assembled application and all its dependencies at rest. Images cannot be changed once compiled. To update an image, you must update the Dockerfile and recreate the image.

Images are uploaded to Docker registries (such as Docker.io, Ghcr.io, or Quay.io) for distribution. Each image has a unique SHA hash, similar to a serial number, that is unique to each image (Ever? Or could be the same if technically the application and all its dependencies were the same?) as a way to ensure that the composition of one image is identical to another. 

The command to upload a Docker image to a registry is `docker push`. To pull an image from a registry, use `docker pull`.

When you run an image using the command `docker run`, it becomes a **container**.

### Container
A container is created from an image every time you use `docker run`. Containers are identical as long as Dockerfile/image is identical, but they are still isolated from one another. 

The container's name is also referred to as its **namespace**. (I need to look into this more, as another source I was looking at made me think otherwise, and now I'm all sorts of confused.)

Each server has to have a Docker engine running in the background and a base OS. 

## Kubernetes (K8s)
Kubernetes is an orchestration tool that manages applications and the containers within them. These applications are often made of hundreds or thousands of containers, and K8s are responsible for managing them across different environments, including physical, VM, and cloud mediums &mdash; or a combination of them.

K8s guarantee:
- High availability
- Scalability
- Disaster recovery/backup and restore

### Clusters
K8s are made of **clusters**. A cluster has three main components: 
  - A **control plane** (also known as the "master node")
  - **Worker nodes** (often referred to as simply "nodes")
  - A **virtual network**

#### Control Plane
The control plane is responsible for running several K8s processes necessary to properly running and managing the cluster.
  - An **API server** &ndash; Serves as an entry point to the cluster through means of a UI, API, or CLI
  - A **controller manager** &ndash; Watches over processes within the cluster
  - A **scheduler** &ndash; Decides on which nodes new pods should be scheduled (deployed) based on workload and server availability
  - An **etcd** &ndash; Holds a current state of the cluster and serves as a backup

The control plane only runs a handful of processes, so it requires less resources than nodes, but it is more important. If the control plane fails, the cluster cannot be accessed; therefore, it is good practice to always have at least one backup (often more).

#### (Worker) Nodes
Nodes are virtual or physical machines that hold containers. Nodes have a higher workload than the control plane and are typically bigger with more resources because they often have hundreds of containers running within them at any given time. 

Each node has a **kublet** process running on it. A kublet is a K8s process that makes it possible for the control plane and the node to talk to each other using the K8s API and execute processes on a node. It also manages pods and containers running on a machine. Each node also has a **container runtime** like Docker that is responsible for pulling the container image from the registry, unpacking the container, and running the application.

Nodes typically have multiple containers. Most work happens in the node where the containers (and their applications) are running.

#### Virtual Network
The virtual network connects the control plane and nodes, allowing them to communicate. The virtual network completes the cluster and turns all nodes into one powerful machine.

### Components
The main components of Kubernetes are:
- **Pod** &ndash; Exists as an abstraction of a container
- **Service** &ndash; Sets static IPs, balances loads, and allows pods to communicate
- **Ingress** &ndash; Routes traffic into clusters
- **ConfigMap** and **Secret** &ndash; Sets external configurations
- **Volume** &ndash Creates data persistence
- **Deployment** and **StatefulSet** &ndash; Replicates pods

#### Pod (`pod`)
A pod is the smallest unit in K8s. It is an abstraction over a container, or a layer on top of it that extracts certain information. K8s use these pods to execute tasks without needing to go through Docker or similar program to do so. K8s does not interact with the actual container, just the layer/pod.

There is usually one pod per application. Each pod has its own IP address that they can use to communicate with each other. However, pods are ephemeral and can "die" easily. When that happens, a new pod must be put in its place, but in doing so, it is assigned a new IP, which is inconvenient as new endpoints must be assigned. Because of this, **services** must be used.

#### Service (`svc`)
Services are static IP addresses; one service is attached to each pod. The lifecycle of pods and services are not connected, so if the pod dies, the IP address stays the same. Services also act as load balancers, catching requests and forwarding them to whichever pod is least busy. Pods also uses services to communicate with each other.

Ideally, applications are accessible through browsers, so they need an **external service**, which opens communications from external sources. But **internal services** are also important, as end users do not need access to certain pods, like databases. The type of service (internal or external) is specified during creation. The default service type is internal.

Service addresses usually look unappealing, like 124.89.101.2:8080. URLs are preferable for end users, which is where an **ingress** comes in.

#### Ingress
An ingress is a static URL that end users use to access an application. An ingress connects to a service. When a user sends a request, it first goes to the ingress, which forwards the request to the service, allowing the user to access the application. 

URLs are often built into applications; therefore, if you need to change an endpoint, you would need to change all of its references, rebuild the image, push it to the repo, pull it into a pod, and restart the container. To avoid this, we use **ConfigMap** and **Secret**.

#### ConfigMap and Secret
ConfigMap and Secret are files that define service endpoints and connect to pods. The pod gets the data that ConfigMap and Secret pass in. If an endpoint needs to be changed, a single line is  modified within the ConfigMap or Secret file rather than all references throughout the application. Data from ConfigMap and Secret is used inside of an application pod via environmental variables or properties.

The determining factor on whether to use ConfigMap or Secret is confidentiality:
- ConfigMap is for *non-confidential data only* and is written in plain-text format.
- Secret is for *confidential data* and stored in base-64 encoded format.

Secrets should still be encrypted by a third-party service, as they are not encrypted when stored in the K8s API underlying data store (etcd). Anyone with API or etcd access can retrieve and modify a Secret. Additionally, anyone who is authorized to create a pod in a namespace can use that access to read any Secret within the namespace; this includes indirect access, such as the ability to create a deployment.

#### Volume
Data should not exist within pods, for if the pod dies, data is lost. This is where **volume** comes in.

Volume is physical storage or a hard drive attached to a pod. Storage can be local (on the same node where the pod is running) or remote, outside of the K8s cluster (cloud, on prem, or hybrid).

Regardless of whether volume is local or remote, they always persist outside of the cluster as an external plugin. K8s does not manage data persistence. The K8s administrator or developer is responsible for backing up data, replicating and managing it, and make sure it is kept on the proper hardware. 

#### Deployment (`deploy`) and StatefulSet
If either the application or database pod dies, or an image needs to be rebuilt, the application is inaccessible to the user, which is why we have clones of nodes that the service redirects traffic to. These clones, or replicants, are connected to the same services as pods, taking advantage of their static IPs and load balancing. 

This is an advantage of distributed systems: rather than relying one one machine, everything is replicated across multiple machines. However, we do not manually work with pods or replicate them; we interact and create a **Deployment** or **StatefulSet** &mdash; a blueprint that tells the pods what to do, such as how many replicants to create.

Just like a pod is a container abstraction, deployment and StatefulSet are abstractions of a pod.

- Deployment is used for state*less* applications.
- StatefulSet is used for state*ful* applications, such as databases. 

Databases and other state-dependent applications should not be replicated with deployment. Since the application and all its replicants would access the same storage (volume), there must be a mechanism controlling which pods are reading and writing to avoid database inconsistencies. Using a StatefulSet ensures that database reads and writes are always synced.

However, deploying StatefulSet can be challenging; therefore, databases are often hosted outside of K8s clusters so that only Deployments and their respective apps are within nodes, making it easy to replicate and scale within clusters as needed.

### Minikube and Kubectl

Production clusters usually have multiple control planes and many nodes. Each node and control plane has to have a virtual or physical machine &mdash; a pain for testing or for running applications on local machines, as there is ample setup required and oftentimes not enough resources, like memory or CPU power. That is where the open-source tool **Minikube** comes in.

#### Minikube
Minikube makes clusters accessible to everyone. Minikube has:
- A one-node cluster
- Master (control plane) and worker (node) processes on the same node &mdash; aka, one machine
- The Docker container runtime pre-installed on the node, allowing you to run pods and containers

Minikube allows us to run clusters, but to communicate with a K8s cluster, we use **kubectl**.

#### Kubectl
Kubectl is the CLI for K8s clusters that can be used to interact with *any type* of K8s cluster setup. 

When you submit a kubectl command create components, it goes to the master node's API server, and the worker nodes carry out the command. The worker nodes create pods and enable them to run on the node. Kubectl can also be used to destroy pods and create services.

#### Usage
You need an applicable driver to run Minikube &mdash; the preferred one is Docker. While Minikube has Docker pre-installed, Minikube *itself* is a container on our local machine, so we need a program like Docker to actually use it. 

Minikube runs as a Docker container, and there is a Docker runtime inside the Minikube cluster to run application containers. Kubectl is installed as a dependency of Minikube, meaning it does not have to be installed separately.

Minikube's primary function is for starting and deleting clusters. Use kubectl to interact with the cluster itself.

### Configuration

All K8s cluster configurations enter through the control plane's API server via a UI, API, or CLI (like kubectl). The request has to be in YAML or JSON format. Config files are stored either with the code of the application/infrastructure or in a separate repository.

Configuration requests are declarative. If we declare we want two replicas of a pod, the controller manager within the control plane ensures that we do; if it sees we only have one replica, it goes to work to produce the desired result. 

There are three parts to K8s config files:
- **Metadata** &ndash; Includes the name of the instance
- **Specification** &ndash; Applicable configurations respective to the component (`kind`)
- **Status** &ndash; Current status of all K8s components automatically generated by the etcd.

The controller manager constantly compares the desired state in the configuration files with the status from the etcd. If they are not the same, K8s will try to fix it. This is the self-healing nature of K8s.

#### ConfigMap &ndash; Config File

| Parameter | Value |
| --- | --- |
| `kind` | `ConfigMap` |
| `metadata: name` | An arbitrary name |
| `data` | The actual contents (key:value pairs) |

#### Secret &ndash; Config File

| Parameter | Value |
| --- | --- |
| `kind` | `Secret` |
| `metadata: name` | An arbitrary name |
| `type` | `Opaque` &ndash; Default. For arbitrary key:value pairs.) |
| `data` | The actual contents (key:value pairs) |

Data values in Secret are base-64 encoded, so plain text can't be used. We have to encode it first.  In the terminal, enter:

```
echo -n <desired text> | base64
```

Paste the result into the file.

#### Deployment and Service &ndash; Config File

Deployment and Service can have separate files, but because each Deployment and StatefulSet need a service, they're usually grouped together in the same file, separated by a `---` line. 

##### Deployment

| Parameter | Value |
| --- | --- |
| `kind` | `Deployment` |
| `template` | Configuration for pod within Deployment config (because Deployment manages pods). Has its own `metadata` and `specs` sections, just like Deployment. |
| `containers` | Which images? Which pods? |

###### ConfigMap and Secret
Use Deployment to pass in information from ConfigMap and Secret.

**ConfigMap YAML**

```
apiVersion: v1
kind: ConfigMap
metadata:
	name: mongo-config
data:
	mongo-url: mongo-service
```

...Passes to web application Deployment.

```
...
env:
- name: USER_NAME                     #predefined by image
  valueFrom:
	  secretKeyRef:
		  name: mongo-secret          #name from secret file
		  key: mongo-user             #data from secret file
- name: USER_PWD                      #predefined by image
  valueFrom:
	  secretKeyRef:
		  name: mongo-secret          #name from secret file
		  key: mongo-password         #data from secret file
- name: DB_URL                        #predefined by image
  valueFrom:
	  secretKeyRef:
		  name: mongo-config          #name from configmap file
		  key: mongo-url              #data from configmap file
```

**Secret YAML**

```
apiVersion: v1
kind: Secret
metadata:
	name: mongo-secret
type: Opaque
data:
	mongo-user: bW9uZ291c2Vy
	mongo-password: bW9uZ29wYXNzd29yZA==
```

...Passes to database Deployment.

```
...
env:
- name: MONGO_INITDB_ROOT_USERNAME    #predefined by image
  valueFrom:
	  secretKeyRef:
		  name: mongo-secret          #name from secret file
		  key: mongo-user             #data from secret file
- name: MONGO_INITDB_ROOT_PASSWORD    #predefined by image
  valueFrom:
	  secretKeyRef:
		  name: mongo-secret          #name from secret file
		  key: mongo-password         #data from secret file
```


###### Labels

You can give any K8s component a label. Labels are key:value pairs attached to K8s resources. They are additional identifiers that should be relevant and meaningful to users.

**Example:**
- `release: stable`
- `env: dev`
- `tier: frontend`

Labels do not provide uniqueness. Each pod replica will have a unique name but the same labels (as long as the pods are from the same Deployment). So, we might have three replicas named mongo-101, mongo-111, and mongo-210, but each can have a label of `app: mongo-db`.

Labels are required for pods. For other components, like Deployment and ConfigMap, they're optional, but it's good practice to use labels.

Label selectors specify which pods belong to which deployment.

```
spec:
	replicas: 3
	selector:
		matchLabels:
			app: mongo-db
```





##### Service

| Parameter | Value |
| --- | --- |
| `kind` | `Service` |
| `metadata: name` | An arbitrary name |
| `selector` | Select pods to forward the requests to (must match a `matchLabel`) |
| `ports` | `port: <service port>` (can be anything); `targetPort: <containerPort of Deployment>` |

The forwarding flow of a Service is:
1. Request (`port: 8080`)*
2. Service (`targetPort: 27017`)
3. Pod (`containerPort: 27017`)

**\*Note:** It is common standard and best practice to make the request's `port` the same as `targetPort` for simplicity.

The default Service type is internal and has a `spec: type` of `ClusterIP`; since it's the default, it does not need to be defined in the config file, but it is what you see in the kubectl terminal. An external Service needs to be defined with a `spec: type` of `NodePort`:

```
spec:
	type: NodePort
```

An external Service also needs a third port (`nodePort`, whereas internal only requires two. The `nodePort` can be any number between 30000 &ndash; 32767. 

```
ports:
	- protocol: TCP
	  port: 3000
	  targetPort: 3000
	  nodePort: 30100
```

#### Important Kubectl Commands

```
kubectl get pod
# shows all pods and their statues

kubectl apply -f <file-name.yaml>
# -f = file
# apply manages applications through files defining K8s resources
# adds files to cluster one by one - deploy config and secret first, then db, then web app

kubectl get all
# see details of all components, OR

kubectl get <component> 
# <component> can be "pod", "configmap", etc.

kubectl get all -o wide
# add "-o wide" to any "get" command for more details

kubectl --help
# lists all kubectl subcommands

kubect <subcommand> -help
# gives help information for subcommand as well as examples

kubectl logs <instance>
# "instance" is a pod name and is optional; gets logs on a pod

kubectl describe <component> <instance>
# "instance" is optional; "describe" commands give additional information on the component; very helpful for filling in log gaps with pods
```

## Miscellaneous 

- **Vertical Scaling** - Making a single machine/server stronger to carry out a certain function.
- **Horizontal Scaling** - Using multiple machines/servers to carry out a certain function.

# Types of Technical Documentation
What is the difference between a procedure? A tutorial? A reference page? Let’s start [here](https://danielsieger.com/blog/2023/04/24/framework-for-better-documentation.html) to know what kind of mind to approach this with.
- **[Tutorial](https://docs.spectrocloud.com/clusters/public-cloud/deploy-k8s-cluster/)** &ndash;   Tutorials are *learning-oriented* and more for beginners. They walk you through how to do something with additional explanations to help build your base of understanding. **Purpose = Learning**.
- **[Procedure](https://docs.spectrocloud.com/clusters/cluster-management/remove-clusters/)** &ndash; Procedures can also be referred to as "help guides." They are for intermediary knowledge of a certain topic. Procedures are *task-oriented*. The scope is more honed and provides steps on how to achieve one thing. It is generally not long. Unnecessary details can be omitted, and it is not designed to give the full picture. **Purpose = Problem solving**.
- **[Reference](https://docs.spectrocloud.com/vertex/install-palette-vertex/install-on-kubernetes/vertex-helm-ref/#required-parameters)** &ndash; Describes in detail how something works. In software, usually API or library references. For experienced audiences since they just need to usually remember or reference something. Should be concise and to the point. **Purpose = Quick information retrieval**.

We want to create a *reference* doc. The audience has basic Kubernetes knowledge. So, this likely means likely H2s, a small blurb (serves as a refresher or quick summaries), and tables. We want to base this off of existing SpectroCloud documentation to maintain consistency.

# Style Guide Reminders
Items that differ from Drake's style guide that I need to remember:
- Avoid present participles and gerunds in headings. 
- Use title caps for acronyms and initialisms. If the term only appears once, do not provide the acronym or initialism in parenthesis. Camel case is acceptable.
- Use first-person and take ownership.