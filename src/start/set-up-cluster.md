# Setup your first Kubernetes Cluster

# 100Days Resources
* [Video by Anais Urlichs](https://youtu.be/uU-8Zcst5Qk)
* [Kubernetes cluster in your local machine using Docker Desktop](https://medium.com/backbase/kubernetes-in-local-the-easy-way-f8ef2b98be68?sk=1cf6c2f31d82d836a2a75503b2fb17be)

# Learning Resources
- What is Kubernetes: [https://youtu.be/VnvRFRk_51k](https://youtu.be/VnvRFRk_51k)
- Kubernetes architecture explained: [https://youtu.be/umXEmn3cMWY](https://youtu.be/umXEmn3cMWY)

# Example Notes

Today, I will get started with the [book](http://leanpub.com/the-devops-2-3-toolkit): The DevOps 2.3 Toolkit; and will work my way through the book.

**Chapter 1** provides an introduction to Kubernetes; I will use it to optimise the notes from the previous day.

**Chapter 2** provides a walkthrough on how to set-up a local Kubernetes cluster using minikube or microk8s. Alternatively, [kind](https://kind.sigs.k8s.io/docs/user/quick-start/) could also be used to create a local Kubernetes cluster or if you have Docker Desktop you could use directly the single node cluster [included](https://medium.com/backbase/kubernetes-in-local-the-easy-way-f8ef2b98be68?sk=1cf6c2f31d82d836a2a75503b2fb17be).

Prerequisites

1. Have Docker installed (if not go ahead and do it): [https://docs.docker.com/](https://docs.docker.com/)
2. Install kubectl 

**Here is how to install kubectl**

If you have the Homebrew package manager installed, you can use that:

```jsx
brew install kubectl
```

On Linux, the commands are going to be:

```jsx
curl -LO [https://storage.googleapis.com/kubernetes-release/release/$](https://storage.googleapis.com/kubernetes-release/release/$)(curl -s https:/\
/storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubec\
tl

chmod +x ./kubectl

sudo mv ./kubectl /usr/local/bin/kubectl
```

To make sure you have kubectl installed, you can run 

```jsx
kubectl version --output=yaml
```

**Install local cluster**

To install minikube, you require a virtualisation technology such as VirtualBox. If you are on windows, you might want to use hyperv instead. Minikube provides a single node instance that you can use in combination with kubectl.

It supports DNS, Dashboards, CNI, NodePorts, Config Maps, etc. It also supports multiple hypervisors, such as Virtualbox, kvm, etc.

In my case I am going to be using [microk8s](https://microk8s.io/) since I had several issues getting started with minikube. However, please don't let this put you off. Please look for yourself into each tool and decide which one you like the best.

Microk8s provides a lightweight Kubernetes installation on your local machine. Overall, it is much easier to install on Linux using snap since it does not require any virtualization tools. 

```jsx
sudo snap install microk8s --classic
```

However, also the Windows and Mac installation are quite straightforward so have a look at those on their website.

Make sure that kubectl has access directly to your cluster.

If you have multiple clusters configured, you can switch between them using your kubectl commands

To show the different clusters available:

```
kubectl config get-contexts
```

to switch to a different cluster:

```
kubectl config use-context <name of the context>
```

Once we are connected to the right cluster, we can ask kubectl to show us our nodes

```
kubectl get nodes
```

Or you could see the current pods that are running on your cluster — if it is a new cluster, you likely don't have any pods running.

```
kubectl get pods
```

In the case of minikube and microk8s, we have only one node
