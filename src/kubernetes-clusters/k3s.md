# K3s and K3sup

# 100Days Resources
* [Video by Anais Urlichs](https://youtu.be/fRcOSqD9p6w)
* Add your blog posts, videos etc. related to the topic here!

# Learning Resources
TODO

# Example Notes

When you are choosing a Kubernetes distribution, the most obvious one is going to be K8s, which is used by major cloud vendors and many more. However, if you just want to play around on your local machine with Kubernetes, test some tools or learn about Kubernetes resources, you would likely go with something like minikube, microk8s or kind. 

Now we just highlighted two use cases for different types of Kubernetes clusters. What about use cases where you want to run Kubernetes on really small devices, such as raspberry pis? What about IoT devices? Generally, devices where you want to run containers effectively without consuming too much resources.

In those cases, you could go with K3s.

## What is K3s?

In short, k3s is half the size in terms of memory footprint than "normal Kubernetes". The origin of k3s is Rio, which was developed by Rancher. However, they then decided to branch it out of Rio into its own tool — which became K3s. It was more of a figure it out by doing it.

The important aspect of k3s is that it was oriented around production right from the beginning.

You want to be able to run Kubernetes in highly resource constraint environments — which is not always possible with pure Kubernetes. 

K3s is currently a CNCF sandbox project — the development is led by Rancher, which provides Kubernetes as a service (?)

- Instead of Docker, it runs Containerd — Note that Kubernetes itself is also moving to Containerd as its container runtime. This does not mean that you will not be able to run Docker containers. If I just scared you, please watch this video to clarify.

**Designed based on the following goals:**

1. Lightweight: Show work on small resource environment
2. Compatibility: You should be able to use most of the tools you can use with "normal k8s"
3. Ethos: Everything you need to use k3s is built right in

*Btw: K3s is described as the second most popular Kubernetes distribution (Read in a Medium post, please don't quote me on this)*

How does k3s differ from "normal" Kubernetes?

[https://youtu.be/FmLna7tHDRc](https://youtu.be/FmLna7tHDRc)

**Do you have questions? We have answers!**

Now I recorded last week an episode with Alex Ellis, who built k3sup, which can be used to deploy k3s. Here are some of the questions that I had before the live recording that are answered within the recording itself:

- Let's hear a bit about the background of k3sup — how did it come about?
- How are both pronounced?
- How would you recommend learning about k3s — let's assume you are complete new, where do you start?
- Walking through the k3s architecture [https://k3s.io/](https://k3s.io/)
- What is the difference between k8s and k3s
- When would I prefer to use k8s over k3s
- What can I NOT do with k3s? Or what would I NOT want to do?
- Do I need a VM to run k3s? It is mentioned in some blog posts — let's assume I do not have a raspberry pi — I have a VM, you can set them up quite easily; why would I run K3s on a VM?
- So we keep discussing that this is great for a Kubernetes homelab or IoT devices — is that not a bit of an overkills to use Kubernetes with it?
- Is the single node k3s similar to microk8s — having one instance that is both worker node

[https://youtu.be/_1kEF-Jd9pw](https://youtu.be/_1kEF-Jd9pw)

**Use cases for k3s:**

- Single node clusters
- Edge
- IoT
- CI
- Development Environments and Test Environments
- Experiments, useful for academia
- ARM
- Embedding K8s
- Situations where a PhD in K8s clusterology is infeasible

### Install k3s

There are three ways for installing k3s*

- The quick way shown below directly with k3s
- Supposedly easier way with k3s up
- The long way that is detailed over several docs pages [https://rancher.com/docs/k3s/latest/en/installation/](https://rancher.com/docs/k3s/latest/en/installation/)

*Actually there are several more ways to install k3s like highlighted in this video:

[https://youtu.be/O3s3YoPesKs](https://youtu.be/O3s3YoPesKs)

This is the installation script:

```jsx
curl -sfL https://get.k3s.io | sh -
```

Note that this might take up to 30 sec. Once done, you should be able to run

```jsx
k3s kubectl get node
```

What it does

> The K3s service will be configured to automatically restart after node reboots or if the process crashes or is killed
Additional utilities will be installed, including kubectl, crictl, ctr, [k3s-killall.sh](http://k3s-killall.sh/), and [k3s-uninstall.sh](http://k3s-uninstall.sh/)
A kubeconfig file will be written to /etc/rancher/k3s/k3s.yaml and the kubectl installed by K3s will automatically use it

Once this is done, you have to set-up the worker nodes that are used by k3s

```jsx
curl -sfL https://get.k3s.io | K3S_URL=https://myserver:6443 K3S_TOKEN=mynodetoken sh -
```

### Once installed, access the cluster

Leverage the KUBECONFIG environment variable:

```
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
kubectl get pods --all-namespaces
helm ls --all-namespaces

```

If you do not set KUBECONFIG as an environment variable,

### Installing with k3sup

The following is taken from the k3sup [READM](https://github.com/alexellis/k3sup)E file

```jsx
$ curl -sLS https://get.k3sup.dev | sh
$ sudo install k3sup /usr/local/bin/
$ k3sup --help
```

There is a lot to unpack... WHAT DOES THIS SENTENCE MEAN?

> This tool uses ssh to install k3s to a remote Linux host. You can also use it to join existing Linux hosts into a k3s cluster as agents.

If you want to get an A to Z overview, watch the following video

[https://youtu.be/2LNxGVS81mE](https://youtu.be/2LNxGVS81mE)

If you are still wondering about what k3s, have a look at this video

[https://youtu.be/-HchRyqNtkU](https://youtu.be/-HchRyqNtkU)

**How is Kubernetes modified?**

- This is not a Kubernetes fork
- Added rootless support
- Dropped all third-party storage drivers, completely CSI is supported and preferred

Following this tutorial; note that there are many others that you could use — have a look at their documentation:

[https://rancher.com/blog/2020/k3s-high-availability](https://rancher.com/blog/2020/k3s-high-availability)