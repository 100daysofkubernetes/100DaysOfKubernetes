# Terminology Primer

This section is intended as a quick primer to get you up to speed with the various terms and acronyms that you are likely to encounter as you begin your Kubernetes journey. This is by no means an exhaustive list and it will certainly evolve over time.

- **Containers:** Containers are a standard package of software that allows you to bundle (or package) an application's code, dependencies, and configuration into a single object which can then be deployed in any environment. Containers isolate the software from it's uderlying environment.

- **Container Image:** According to [Docker](https://www.docker.com/resources/what-container), a Container Image *"is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries and settings."* Container Images become Containers at runtime.

- **Container Registry:** A container registry is a repository for storing all of your container images. Examples of containers registries are: 'Azure Container Registry', 'Docker Hub', and 'Amazon Elastic Container Registry'.

- **Docker:** Docker is an open-source platform used for automating the deployment of containerised applications.

- **Kubernetes:** Also know as **K8s**, Kubernetes is an open-source system for automating the deployment, scaling, and management of containerised applications. Please see [kubernetes.io](https://kubernetes.io/) for more details. It could also be thought of as a 'Container Orchestrator'.

- **Control Plane:** The control plane is the container orchestration layer. It exposes an API which allows you to manage your cluster and it's resources.

- **Namespaces:** Namespaces are units of organisation. They allow you to group related resources.

- **Nodes:** Nodes are worker machines in Kubernetes. Nodes can be virtual or physical. Kubernetes runs workloads by placing containers into Pods which run on Nodes. You will typically have multiple nodes in your Kubernetes cluster.

- **Pods:** Pods are the smallest deployable unit of compute that you can create and manage in Kubernetes. A pod is a group of one or more containers.

- **Service:** A Service in Kubernetes is a networking abstraction for Pod access. It handles the network traffic to a Pod or set of Pods.

- **Cluster:** A Kubernetes Cluster is a set of nodes.

- **Replica Sets:** A Replica Set works to ensure that the defined number of Pods are running in the Cluster at all times.

- **Kubectl:** Kubectl is a command-line tool for interacting with a Kubernetes API Server to manage your Cluster.

- **etcd:** Etcd is a key value store that Kubernetes uses to store Cluster data.

- **Ingress:** An object that manages external access to the services running on the Cluster.

- **K3s:** K3s is a lightweight Kubernetes distribution designed for IoT or Edge computing scenarios.

- **GitOps:** According to [Codefresh](https://codefresh.io/gitops/), *"GitOps is a set of best-practices where the entire code delivery process is controlled via Git, including infrastructure and application definition as code and automation to complete updates and rollbacks."*

- **Containerd:** Containerd is a container runtime that manages the complete container lifecycle.

- **Service Mesh:** According to [Istio](https://istio.io/latest/docs/concepts/what-is-istio/), a Service Mesh describes the network of micro-services within an application and the communications between them.

- **AKS:** Azure Kubernetes Service (AKS) is a managed, hosted, Kubernetes service provided by Microsoft Azure. A lot of the management of your Kubernetes cluster is abstracted away and managed by Azure. Find out more [here](https://docs.microsoft.com/en-us/azure/aks/intro-kubernetes).

- **EKS:** Elastic Kubernetes Service (EKS), provided by Amazon AWS, is a managed, hosted, Kubernetes service. Similar to AKS, much of the management is abstracted away and managed by the cloud provider. Find out more [here](https://aws.amazon.com/eks)

- **GKE:** Google Kubernetes Engine (GKE), is another managed Kubernetes service. This time from Google. Find out more [here](https://cloud.google.com/kubernetes-engine/docs/concepts/kubernetes-engine-overview).

- **Helm:** Helm can be thought of as a Package Manager for Kubernetes.

- **Helm Chart:** Helm Charts are YAML files that define, install and upgrade Kubernetes applications.

