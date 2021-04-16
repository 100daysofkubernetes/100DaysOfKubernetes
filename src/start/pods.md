# Kubernetes Pods 

# 100Days Resources
* [Video by Anais Urlichs](https://youtu.be/fCpv7xSEyEI)
* Add your blog posts, videos etc. related to the topic here!

# Learning Resources

- [https://www.vmware.com/topics/glossary/content/kubernetes-pods](https://www.vmware.com/topics/glossary/content/kubernetes-pods)
- [https://cloud.google.com/kubernetes-engine/docs/concepts/pod](https://cloud.google.com/kubernetes-engine/docs/concepts/pod)
- [https://kubernetes.io/docs/concepts/workloads/pods/](https://kubernetes.io/docs/concepts/workloads/pods/)

# Example Notes

**Overview**

Pods are the smallest unit in a Kubernetes cluster; which encompass one or more application ⇒ it represents processes running on a cluster. Pods are used to manage your application instance.

Here is a quick summary of what a Pod is and its responsibilities:
- In our nodes, and within our Kubernetes cluster, the smallest unit that we can work with are pods.
- Containers are part of a larger object, which is the pod. We can have one or multiple containers within a pod.
- Each container within a pod share an IP address, storage and namespace — each container usually has a distinct role inside the pod.
- Note that pods usually operate on a higher level than containers; there are more of an abstraction of the processes within a container than the container itself.
- A pod can also run multiple containers; all containers are started in parallel ⇒ this makes it difficult to know which process started before another.
- Usually, one pod is used per container process; reasons to run two containers within a pod might be logging purposes.
- *nitContainers* can be used to ensure some containers are ready before others in a pod. To support a single process running in a container, you may need logging, a proxy, or special adapter. These tasks are often handled by other containers in the same Pod.
- Usually each pod has one IP address
- You may find the term *sidecar* for a container dedicated to performing a helper task, like handling logs and responding to requests, as the primary application container may have this ability.

**Running multiple containers in one pod**

An example for running multiple containers within a pod would be an app server pod that contains three separate containers: the app server itself, a monitoring adapter, and a logging adapter. Resulting, all containers combines will provide one service.

In this case, the logging and monitoring container should be shared across all projects within the organisation.

**Replica sets**

Each pod is supposed to run a single instance of an application. If you want to scale your application horizontally, you can create multiple instance of that pod.

It is usually not recommended to create pods manually but instead use multiple instances of the same application; these are then identical pods, called replicas.

Such a set of replicated Pods are created and managed by a controller, such as a Deployment.

**Connection**

All the pods in a cluster are connected. Pods can communicate through their unique IP address. If there are more containers within one pod, they can communicate over localhost.

**Pods are not forever**

Pods are not "forever"; instead, they easily die in case of machine failure or have to be terminated for machine maintenance. When a pod fails, Kubernetes automatically (unless specified otherwise) spins it up again.

Additionally, a controller can be used to ensure that the pod is "automatically" healing. In this case, the controlled will monitor the stat of the pod; in case the desired state does not fit the actual state; it will ensure that the actual state is moved back towards the desired state.

It is considered good practice to have one process per pod; this allows for easier analysis and debugging.

**Each pod has:**

- a unique IP address (which allows them to communicate with each other)
- persistent storage volumes (as required) (more on this later on another day)
- configuration information that determine how a container should run

**Pod lifecycle**

(copied from Google)

Each Pod has a PodStatus API object, which is represented by a Pod's status field. Pods publish their phase to the status: phase field. The phase of a Pod is a high-level summary of the Pod in its current state.

When you run
`kubectl get pod` [Link](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) 
to inspect a Pod running on your cluster, a Pod can be in one of the following
possible phases:

- **Pending:** Pod has been created and accepted by the cluster, but one or more
of its containers are not yet running. This phase includes time spent being
scheduled on a node and downloading images.
- **Running:** Pod has been bound to a node, and all of the containers have been
created. At least one container is running, is in the process of starting, or
is restarting.
- **Succeeded:** All containers in the Pod have terminated successfully.
Terminated Pods do not restart.
- **Failed:** All containers in the Pod have terminated, and at least one
container has terminated in failure. A container "fails" if it exits with a
non-zero status.
- **Unknown:** The state of the Pod cannot be determined.

**Limits**

Pods by themselves do not have a memory or CPU limit. However, you can set limits to control the amount of CPU or memory your Pod can use on a node. A limit is the maximum amount of CPU or memory that Kubernetes guarantees to a Pod.

**Termination**

Once the process of the pod is completed, it will terminate. Alternatively, you can also delete a pod.

