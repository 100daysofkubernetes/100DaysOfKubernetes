# GitOps and Argo

# 100Days Resources
* [Video by Anais Urlichs](https://youtu.be/c4v7wGqKcEY)
* Add your blog posts, videos etc. related to the topic here!

# Learning Resources
TODO

# Example Notes

A few months ago, I wrote an article ["Understanding GitOps: The Latest Tools and Philosophies"](https://thenewstack.io/understanding-gitops-the-latest-tools-and-philosophies/) for the New Stack. In that blog post, I went over the basics of what GitOps is all about and the key facts as of today. 

So what is GitOps and why would we want to use it? In its simplest form, GitOps is the principle of defining all of our resources in Git. Meaning, anything that we could possibly push to a Git repository and keep version controlled, we should.

**Why do we want to learn about GitOps and implement it in our organisation?**

There are different ways to duplicate steps on different machines. We could either go ahead and press different buttons, and detail the process of us setting everything up, or we could go ahead and define our Infrastructure as Code, Kubernetes resources through declarative YAML files and so on.

Using the declarative way to define our resources makes our infrastructure pro-active rather than reactive. This about, instead of zipping through different TV channels, you tell your TV that every Saturday morning at 10 AM you want to watch a cooking show by person x. Ultimately, your TV is going to take care of making that happen.

The main problem with the imperative approach of setting up our resources is that we cannot accurately recreate the same steps. UIs change, buttons move, so how can we ensure that we press the same buttons always.

Additionally, setting up resources the manual way is extremely risky. How do you ensure people do not break things, that the security is correct and the networking is in place?

**So how does GitOps actually work?**

The two most popular tools right now for GitOps best practices are Flux CD and Argo CD. In both cases, you will install an agent inside your cluster. This agent is then responsible for monitoring your resources.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a73d3f55-2842-4727-9286-9b7f8f70d1aa/GitOps.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a73d3f55-2842-4727-9286-9b7f8f70d1aa/GitOps.png)

The desired state of your resources will be defined in Git. Whenever anything changes to your desired state, the Agent will pull the new resources from Git and deploy them to your Kubernetes Cluster. You can call it magic or really good engineering work 🙂

**Now why is there a pull model instead of a push model?** 

The pull model makes it possible for the agent to deploy new images from inside the cluster; they are pulling information into the cluster rather than the cluster having to allow new information being pushed in from the outside. The latter would open the cluster for new security risks.

## Getting Started

In this example, we are going to be using Argo CD to install resources in our cluster. Why are we using Argo and not Flux? I am familiar with Argo but not so familiar with Flux. Additionally, Argo has a really nice UI that will help us understand what is actually happening within our cluster.

Separately, ArgoCD just launched its version 2.0. Note that I will be trying out their 2.0 version for the first time. [https://www.cncf.io/blog/2021/04/07/argo-cd-2-0-released/](https://www.cncf.io/blog/2021/04/07/argo-cd-2-0-released/)

Documentation: [https://argoproj.github.io/argo-cd/](https://argoproj.github.io/argo-cd/)

So, let's get started.

Create a new cluster

```jsx
kind create cluster --name argocd
```

Let's create a namespace for agrocd

```jsx
kubectl create namespace argocd
```

And install the CRDs that Argo is based on 

```jsx
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v2.0.0-rc3/manifests/install.yaml
```

Argo is installed as CRD inside our cluster

```jsx
kubectl get all -n argocd
```

Now that we have that, we want to access the UI.

```jsx
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

You can get the password through the following command. Make sure that you port-forwarded beforehand.

```jsx
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

And then log into 

```jsx
argocd login localhost:8080
```

This will allow us to update our password — the username is "admin"

```jsx
argocd account update-password
```

We will open [localhost](http://localhost) next and log into the UI.

Let's deploy an application. First, through the UI and then through the CLI. You can install the Argo CD CLI through the following commands:

```jsx
VERSION=$(curl --silent "https://api.github.com/repos/argoproj/argo-cd/releases/latest" | grep '"tag_name"' | sed -E 's/.*"([^"]+)".*/\1/')
curl -sSL -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/download/$VERSION/argocd-linux-amd64
chmod +x /usr/local/bin/argocd
```

Here is the documentation: [https://argoproj.github.io/argo-cd/cli_installation/](https://argoproj.github.io/argo-cd/cli_installation/)

We are going to be using the following repository

[https://github.com/AnaisUrlichs/react-article-display](https://github.com/AnaisUrlichs/react-article-display)

Now we will go ahead and tell the Argo UI about my Helm Chart. If you already have a repository with a Helm Chart, YAML files, or any other YAML manifests, you could use that instead.

Alternatively, you can tell Argo CD about the repository through the following command or similar:

```jsx
argocd app create react-app --repo [https://github.com/anais-codefresh/react-article-display](https://github.com/anais-codefresh/react-article-display).git --path charts/example-chart --dest-server https://kubernetes.default.svc --dest-namespace default
```

Once the app has been deployed, we can take a look 

```jsx
kubectl get all
```

```jsx
argocd app get react-app
```

We can also ensure that our desired state is synced with the actual state in our cluster

```jsx
argocd app sync react-app
```

So what happens if we make any manual changes with the resources within our cluster?

Let's go ahead and patch the service into type NodePort

```jsx
kubectl patch svc react-app-example-chart --type='json' -p '[{"op":"replace","path":"/spec/type","value":"NodePort"}]'
```

And check the status again

```jsx
argocd app get react-app
```

## Additional Resources

If you do not have enough time for all of this, here is a video in which I provide an overview of GitOps in about 1 min

[https://youtu.be/H7wex_SmtrI](https://youtu.be/H7wex_SmtrI)