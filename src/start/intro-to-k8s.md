# What is Kubernetes and why do we want it?

# 100Days Resources
* [Video by Anais Urlichs](https://youtu.be/fCpv7xSEyEI)
* Add your blog posts, videos etc. related to the topic here!

# Learning Resources
- [What is Kubernetes](https://youtu.be/VnvRFRk_51k)
- [Kubernetes architecture explained](https://youtu.be/umXEmn3cMWY)
- [Container Orchestration Explained](https://youtu.be/kBF6Bvth0zw)

# Example Notes

Kubernetes builds on Borg and the lessons learned by Google for operating Borg for over 15 years. 

Borg was used by Google internally to manage its systems.

For more information here is the full paper [https://research.google/pubs/pub43438/](https://research.google/pubs/pub43438/)

**What is Kubernetes?**

Kubernetes is an open-source container Orchestration Framework.

At its root, it manages containers — to manage applications that are made of of containers- physical machines, virtual machines, hybrid environments

According to the [kubernetes.io](https://kubernetes.io/) website, Kubernetes is:

*"an open-source system for automating deployment, scaling, and management of containerized applications"*.

**What problems does it solve**

- Following the trend from Monolithic to Microservices — traditionally, an application would be a Monolithic application — which requires the hardware to scale with the application. In comparison, Kubernetes deploys a large number of small web servers.
- Containers are the perfect host for small self-contained applications
- Applications comprised of 100s of containers — managing those with scripts can be really difficult and even impossible
- Kubernetes helps us with the following: connecting containers across multiple hosts, scaling them, deploying applications without downtime, and service discovery among several other aspects

The benefits of splitting up your application from Monolithic into Microservices is that they are easier to maintain. For instance:

Instead of a large Apache web server with many httpd daemons responding to page requests, there would be many nginx servers, each responding.

Additionally, it allows us to separate matters of concerns within our application i.e. decoupling the architecture based on responsibilies.

Additionally, Kubernetes is an essential part of 

- Continuous Integration
- Continuous Delivery

**Orchestration tools such as Kubernetes offer:**

- High availability
- Scalability: Applications have higher performance e.g. load time
- Disaster Recovery: The architecture has to have a way to back-up the data and restore the state of the application at any point in time

**How does the architecture actually look like:**

- You have a master node: Runs several Kubernetes processes that are necessary to run the container's processes — e.g. an **API server** ⇒ the entry point to the Kubernetes cluster (UI, API, CLI); then it needs to have a **Controller Manager** ⇒ keeps track of what is happening e.g. detects when a pod dies ⇒ detects state changes; and lastly, it contains a **Scheduler** ⇒ this ensures the Pod placement (more on pods later); **etcd database** ⇒ key-value storage that holds the state of the Kubernetes cluster at any point in time; and the last thing that is needed is the **Virtual Network** that spans across all the nodes in the cluster
- And worker nodes: contains Containers of different applications; here is where the actual work is happening

Note that worker nodes are usually much bigger because they are running the containers. The master node will only run a selection of processes. 

Each node will have multiple pods with containers running on them. 3 processes have to be present on all nodes

- Container Runtime e.g. Docker
- Kubelet; which is a process of Kubernetes itself that interacts with both, the container runtime and the node — it is responsible for taking our configuration and starting a pod inside the node

Usually a Kubernetes cluster has several nodes running in parallel.

- nodes communicate with services between each other. Thus, the third process that has to be installed on every node is Kube Proxy that forwards requests between nodes and pods— the communication works in a performant way with low overhead. Requests are forwarded to the closest pod.

**Kubernetes Concepts — Pods**

Pod ⇒ the smallest unit that you, as a Kubernetes user will configure

- Pods are a wrapper of a container; on each worker node, we will have multiple containers
- Usually, you would have one application, one container per pod
- Each Pod has its own IP address — thus, each pod is its own self-containing server. This allows pods to communicate with each other — through their internal IP addresses

Note that we don't actually create containers inside the Kubernetes cluster but we work with the pods that are an abstraction layer over the containers. Pods manage the containers without our intervention

However pods can die very frequently

- Whenever a pod dies, it will be recreated and it will get a new IP address

A service is used as an alternative for a pods IP address. Resulting, there is a service that sits in front of each pod that abstracts away the dynamic IP address. Resulting, the lifecycle of a pod and the IP address are not tied to each other-

If a pod behind the service dies, it gets recreated.

A service has two main functions:

- Providing an IP address to the pod(s)
- It is a Loadbalancer (what the hack is that 😆 — more on this later)

**How do we create those components**

- All configuration goes through the master node — UI, API, CLI, all talk to the APi server within the master node — they send the configuration request to the API server
- The configuration is usually in YAML format ⇒ a blue print for creating pods. The Kubernetes agents convert the YAML to JSON prior to persistence to the database.
- The configuration of the requirements are in a declarative format. This will allow it to compare the desired state to the actual state (more on this later)

**Developer Workflow:**

- Create Docker images based on your application
- Use Docker and Kubernetes
- A CI pipeline to build, test, and verify Docker images
- "You must be able to perform rolling updates and rollbacks, and eventually tear down the resource when no longer needed." — the course

This requires flexible and easy to use network storage.