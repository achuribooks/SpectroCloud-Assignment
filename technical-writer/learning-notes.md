
# Important Terms and Concepts
## Kubernetes (K8s)
### Overview
Kubernetes is an orchestration tool that manages applications and the containers within them. These applications are often made of hundreds or thousands of containers, and K8s are responsible for managing them across different environments, including physical, VM, and cloud mediums &mdash; or a combination of them.

K8s guarantee:
- High availability
- Scalability
- Disaster recovery/backup and restore

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

### Basic Setup
#### Pod
A pod is the smallest unit in K8s. It is an abstraction over a container, or a layer on top of it that extracts certain information. K8s use these pods to extract the container's runtime or technology so that it can be replaced without needing to go through Docker or similar program to do so. K8s does not interact with the actual container, just the layer/pod.

There is usually one pod per application. Each pod has its own IP address that they can use to communicate with each other. However, pods are ephemeral and can "die" easily. When that happens, a new pod must be put in its place, but in doing so, it is assigned a new IP, which is inconvenient as new endpoints must be assigned. Because of this, **services** must be used.

#### Service
Services are static IP addresses; one service is attached to each pod. The lifecycle of pods and services are not connected, so if the pod dies, the IP address stays the same.

Ideally, applications are accessible through browsers, so they need an **external service**, which open communications from external sources. But **internal services** are also important, as end users do not need access to certain pods, like databases. The type of service (internal or external) is specified during creation.

Service addresses usually look unappealing, like 124.89.101.2:8080. URLs are preferable for end users, which is where an **ingress** comes in.

#### Ingress
An ingress is a static URL that end users use to access an application. An ingress connects to a service. When a user sends a request, it first goes to the ingress, which forwards the request to the service, allowing the user to access the application. 





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