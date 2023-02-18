# Kubernetes Architecture

# 100Days Resources
- [Video by Anais Urlichs](https://youtu.be/oqWgMc9yYcc)
- Add your blog posts, videos etc. related to the topic here!

# Learning Resources
- [How Kubernetes deployments work](https://youtu.be/mNK14yXIZF4)

# Example Notes

We can divide the responsibilities within a Kubernetes cluster between a main node and worker nodes. Note that in small clusters we may have one node that takes the responsibilities of both.

## Main Node

Where does the orchestration from Kubernetes come in? These are some characteristics that make up Kubernetes as a container orchestration system:

- Managed by several operators and controllers — will look at operators and controllers later on. [Operators](https://kubernetes.io/docs/concepts/extend-kubernetes/operator/) make use of custom resources to manage an application and their components.
- "Each controller interrogates the kube-apiserver for a particular object state, modifying the object until the declared state matches the current state." In short, [controllers](https://kubernetes.io/docs/concepts/architecture/controller/) are used to ensure a process is happening in the desired way.
- "The [ReplicaSet](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) is a controller which deploys and restarts containers, Docker by default, until the requested number of containers is running." In short, its purpose is to ensure a specific number of pods are running.

Note that those concepts are details in further sections of the book.

There are several other API objects which can be used to deploy pods. A DaemonSet will ensure that a single pod is deployed on every node. These are often used for logging and metrics. A StatefulSet can be used to deploy pods in a particular order, such that following pods are only deployed if previous pods report a ready status.

API objects can be used to know

- What containerized applications are running (and on which nodes)
- The resources available to those applications
- The policies around how those applications behave, such as restart policies, upgrades, and fault-tolerance

- **kube-apiserver**
    - Provides the front-end to the cluster's shared state through which all components interact
    - Is central to the operation of the Kubernetes cluster.
    - Handles internal and external traffic
    - The only agent that connects to the etcd database
    - Acts as the master process for the entire cluster
    - Provides the out-wards facing state for the cluster's state
    - Each API call goes through three steps: 
    authentication, authorization, and several admission controllers.
- **kube-scheduler**
    - The Scheduler sees a request for running a container and will run the container in the best suited node
    - When a new pod has to be deployed, the kube-scheduler determines through an algorithm to which node the pod should be deployed
    - If the pod fails to be deployed, the kube-scheduler will try again based on the resources available across nodes
    - A user could also determine which node the pod should be deployed to —this can be done through a custom scheduler
    - Nodes that meet scheduling requirements are called feasible nodes.
    - You can find more [details about the scheduler on GitHub.](https://github.com/kubernetes/kubernetes/blob/master/pkg/scheduler/scheduler.go)
- **etcd Database**
    - The state of the cluster, networking, and other persistent information is kept in an etcd database
    - etcd is a consistent and highly-available key value store used as Kubernetes' backing store for all cluster data
    - Note that this database does not change; previous entries are not modified and new values are appended at the end.
    - Once an entry can be deleted, it will be labelled for future removal by a compaction process. It works with curl and other HTTP libraries and provides reliable watch queries.
    - Requests to update the database are all sent through the kube api-server; each request has its own version number which allows the etcd to distinguish between requests. If two requests are sent simultaneously, the second request would then be flagged as invalid with a 409 error and the etcd will only update as per instructed by the first request.
    - Note that it has to be specifically configured
- **Other Agents**
    - The kube-controller-manager is a core control loop daemon which 
    interacts with the kube-apiserver to determine the state of the cluster.
     If the state does not match, the manager will contact the necessary 
    controller to match the desired state. 
    It is also responsible to interact with third-party cluster management and reporting.
    - The cluster has several controllers in 
    use, such as endpoints, namespace, and replication. The full list has 
    expanded as Kubernetes has matured. Remaining in beta as of 
    v1.16, the cloud-controller-manager interacts with agents outside of the
     cloud. It handles tasks once handled by kube-controller-manager. This 
    allows faster changes without altering the core Kubernetes control 
    process. Each kubelet must use the **--cloud-provider-external** settings passed to the binary.
- There are several add-ons which have become essential to a typical 
production cluster, such as DNS services. Others are third-party 
solutions where Kubernetes has not yet developed a local component, such
 as cluster-level logging and resource monitoring.

"Each node in the cluster runs two processes: a kubelet and a kube-proxy."

Kubelet: handles requests to the containers, manages resources and looks after the local nodes

The Kube-proxy creates and manages networking rules — to expose container on the network

- **kubelet**

    Each node has a container runtime e.g. the Docker engine installed. The kubelet is used to interact with the Docker Engine and to ensure that the containers that need to run are actually running.

    Additionally, it does a lot of the work for the worker nodes; such as accepting API calls for the Pods specifications that are either provided in JSON or YAML. 

    Once the specifications are received, it will take care of configuring the nodes until the specifications have been met

    Should a Pod require access to **storage**, **Secrets** or **ConfigMaps**, the kubelet will ensure access or creation. It also sends back status to the kube-apiserver for eventual persistence.

- **kube-proxy**

    The kube-proxy is responsible for managing the network connectivity to containers. To do that is has iptables. 

    "[iptables](https://en.wikipedia.org/wiki/Iptables) is a user-space utility program that allows a system administrator to configure the IP packet filter rules of the Linux kernel firewall, implemented as different Netfilter modules."

    Additional options are the use of namespaces to monitor services and endpoints, or ipvs to replace the use of iptables

To easily manage thousands of Pods across hundreds of nodes can be a difficult task to manage. To make management easier, we can use labels, arbitrary strings which become part of the object metadata. These can then be used when checking or changing the state of objects without having to know individual names or UIDs. Nodes can have taints to discourage Pod assignments, unless the Pod has a toleration in its metadata.

**Multi-tenancy**

When multiple-users are able to access the same cluster

**Additional security measures can be taken through either of the following:**

1. **Namespaces**: Namespaces can be used to "divide the cluster"; additional permissions can be set on each namespace; note that two objects cannot have the same name in the same namespace
2. **Context**: A combination of user, cluster name and namespace; this allows you to restrict the cluster between permissions and restrictions. This information is referenced by ~/.kube/config

    Can be checked with

    ```jsx
    kubectl config view
    ```

3. **Resource Limits:** Provide a way to limit the resources that are provided for a specific pod
4. **Pod Security Policies**: "A policy to limit the ability of pods to elevate permissions or modify the node upon which they are scheduled. This wide-ranging limitation may prevent a pod from operating properly. The use of PSPs may be replaced by Open Policy Agent in the future."
5. **Network Policies:** The ability to have an inside-the-cluster firewall. 
Ingress and Egress traffic can be limited according to namespaces and labels as well as typical network traffic characteristics.
