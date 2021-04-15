# KinD

# 100Days Resources
* [Video by Anais Urlichs](https://youtu.be/Bdw5saYQMvY)
* Add your blog posts, videos etc. related to the topic here!

# Learning Resources
TODO

# Example Notes

## Setting up Kind on Windows and Cluster Comparison

So, I just got started setting up everything on Windows since Ubuntu provides me with some limitations regarding my streaming set-up (also, I got bullied away from Ubuntu) so here I am making the best out of Windows 😁

These are the docs that I used to set-up all of my resources on Windows:

**Setting up the Ubuntu in Windows**

- [https://docs.microsoft.com/en-us/windows/wsl/install-win10#step-4---download-the-linux-kernel-update-package](https://docs.microsoft.com/en-us/windows/wsl/install-win10#step-4---download-the-linux-kernel-update-package)
- [https://github.com/microsoft/WSL/issues/4766](https://github.com/microsoft/WSL/issues/4766)

**Use WSL in Code**

- [https://docs.docker.com/docker-for-windows/wsl/](https://docs.docker.com/docker-for-windows/wsl/)
- [https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl)

**Kubectl installation**

- [https://devkimchi.com/2018/06/05/running-kubernetes-on-wsl/](https://devkimchi.com/2018/06/05/running-kubernetes-on-wsl/)

**Create Kind cluster**

- [https://kubernetes.io/blog/2020/05/21/wsl-docker-kubernetes-on-the-windows-desktop/](https://kubernetes.io/blog/2020/05/21/wsl-docker-kubernetes-on-the-windows-desktop/)

If you want to run microk8s on WSL, you have to get a snap workaround described here

- [https://github.com/microsoft/WSL/issues/5126](https://github.com/microsoft/WSL/issues/5126)

## Running a local cluster

Especially when you are just getting started, you might want to spin up a local cluster on your machine. This will allow you to run tests, play around with the resources and more without having to worry much about messing something up :) — If you watched any of my previous videos, you will have already a good understand of how much I enjoy trying out different things — setting things up just to try something out — gain a better understanding — and then to delete everything in the next moment.

Also, you might have seen that I already have two videos on microk8s —mainly since the minikube set-up on my Ubuntu did not properly work. Now that I am on Windows, I might actually have more options. So let's take a look at those and see how they compare.

## Kubernetes in Docker

When you install Docker for Desktop (or however you call it) you can enable Kubernetes:

The set-up will take a few seconds but then you have access to a local cluster both from the normal Windows terminal (I am still new to Windows, so excuse any terminology that is off) or through the WSL.

```jsx
kubectl config get-contexts
```

and you will see a display of the different clusters that you have set-up. This will allow you to switch to a different cluster:

```jsx
kubectl config use-context <context name>
```

Below are several other options highlighted.

## **minikube**

minikube is probably the best known of the three; maybe because it is the oldest. When you are using minikube, it will spin up a VM that runs a single Kubernetes node. To do so it needs hypervisor. Now, if you have never interacted with much virtualisation technology, you might think of a hypervisor as something like this:

I assure you, it is not. So what is a Hypervisor then? A Hypervisor is basically a form of software, firmware, or hardware that is used to set-up virtual environments on your machine.

Running minikube, you can spin up multiple nodes as well, each will be running their own VM (Virtual Machine).

For those of you, who are really into Dashboards, minikube provides a Dashboard, too! Personally, I am not too much of a fan but that is your choice. If you would like some sort of Dashboard, I highly recommend you k9s. [Here is my introductory video if you are curious.](https://youtu.be/5Nhbl6LwP2o)

This is what the minikube Dashboard looks like — just your usual UI 😊

Now how easy is the installation of minikube? Yes, I mentioned that I had some problems installing minikube on Ubuntu and thus, went with microk8s. At that time, I did not know about kind yet. Going back to the original question, if you are using straight up Windows it is quite easy to install, if you are using Linux in Windows however, it might be a bit different — tbh I am a really impatient person, so don't ask me.

[Documentation](https://minikube.sigs.k8s.io/docs/start/)

## What is kind?

Kind is quite different to minikube, instead of running the nodes in VMs, it will run nodes as Docker containers. Because of that, it is supposed to start-up faster — I am not sure how to test this, they are both spinning up the cluster in an instance and that is good enough for me.

However, note that kind requires more space on your machine to run than etiher microk8s or minikube. In fact, microk8s is actually the smallest of the three. 

[Like detailed in this article, you can](https://brennerm.github.io/posts/minikube-vs-kind-vs-k3s.html)

- With 'kind load docker-image my-app:latest' the image is available for use in your cluster

Which is an additional feature.

If you decide to use kind, you will get the most out of it if you are fairly comfortable to use YAML syntax since that will allow you to define different cluster types. 

[Documentation](https://kind.sigs.k8s.io/docs/user/quick-start/)

[The documentation that I used to install it](https://kubernetes.io/blog/2020/05/21/wsl-docker-kubernetes-on-the-windows-desktop/)

## microk8s

Microk8s is in particular useful if you want to run a cluster on small devices; it is better tested in ubuntu than the other tools. Resulting, you can install it with snap super quickly! In this case, it will basically run the cluster separate from the rest of the stuff on your computer. 

It also allows for multi-node clusters, however, I did not try that yet, so I don't know how well that actually works. 

Also note that if you are using microk8s on MacOS or Windows, you will need a hypevisor of sorts. Running it on Ubuntu, you do not.

[Documentation](https://microk8s.io/)

My video

[https://youtu.be/uU-8Zcst5Qk](https://youtu.be/uU-8Zcst5Qk)

## Direct comparison

This article by Max Brenner offers a really nice comparison between the different tools with a comparison table [https://brennerm.github.io/posts/minikube-vs-kind-vs-k3s.html](https://brennerm.github.io/posts/minikube-vs-kind-vs-k3s.html)

