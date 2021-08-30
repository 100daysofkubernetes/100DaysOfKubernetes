# GitOps and Argo

# 100Days Resources
* [Video by Anais Urlichs on ArgoCD](https://youtu.be/c4v7wGqKcEY)
* [Video by Anais Urlichs on Argo Workflows](https://youtu.be/c3qJr6L8nHg)
* Add your blog posts, videos etc. related to the topic here!

# Learning Resources
- ["Understanding GitOps: The Latest Tools and Philosophies"](https://thenewstack.io/understanding-gitops-the-latest-tools-and-philosophies/)
- [The DevOps Toolkit](https://www.youtube.com/c/DevOpsToolkit/videos) has several great explanations and tutorials around GitOps and ArgoCD

# Example Notes

This is the website of Argo [https://argoproj.github.io/](https://argoproj.github.io/)

Argo is currently a CNCF incubating project. Part of the Argo project family are three different projects:

1. [Continuous Delivery](./argo.html#argo-cd)
2. [Workflows and Pipelines](./argo.html#argo-workflows)
3. [Events](./argo.html#argo-events)
4. Rollouts

**So why would you want to learn about Argo and all of its projects?**

Argo follows best-practices for deploying and monitoring your applications as well as any resources that your application may depend on. Additionally, it is open source, so it will allow you to become familiar with best practices for deployments without having to pay for an expensive tool.

A lot of employers want to see your experience with real-world tools and scenarios. If you do not have access directly to the paid tools that are used in production, your best chance is to get really familiar with the open source tools that are used and available.

## Argo CD

A few months ago, I wrote an article ["Understanding GitOps: The Latest Tools and Philosophies"](https://thenewstack.io/understanding-gitops-the-latest-tools-and-philosophies/) for the New Stack. In that blog post, I went over the basics of what GitOps is all about and the key facts as of today. 

So what is GitOps and why would we want to use it? In its simplest form, GitOps is the principle of defining all of our resources in Git. Meaning, anything that we could possibly push to a Git repository and keep version controlled, we should.

**Why do we want to learn about GitOps and implement it in our organisation?**

There are different ways to duplicate steps on different machines. We could either go ahead and press different buttons, and detail the process of us setting everything up, or we could go ahead and define our Infrastructure as Code, Kubernetes resources through declarative YAML files and so on.

Using the declarative way to define our resources makes our infrastructure pro-active rather than reactive. This about, instead of zipping through different TV channels, you tell your TV that every Saturday morning at 10 AM you want to watch a cooking show by person x. Ultimately, your TV is going to take care of making that happen.

The main problem with the imperative approach of setting up our resources is that we cannot accurately recreate the same steps. UIs change, buttons move, so how can we ensure that we press the same buttons always.

Additionally, setting up resources the manual way is extremely risky. How do you ensure people do not break things, that the security is correct and the networking is in place?

**So how does GitOps actually work?**

The two most popular tools right now for GitOps best practices are Flux CD and Argo CD. In both cases, you will install an agent inside your cluster. This agent is then responsible for monitoring your resources.

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
argocd app create react-app --repo https://github.com/anais-codefresh/react-article-display.git --path charts/example-chart --dest-server https://kubernetes.default.svc --dest-namespace default
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


## Argo Events

Documentation: [https://argoproj.github.io/argo-events/](https://argoproj.github.io/argo-events/)

Argo events is an event-driven workflow automation framework. This means that there is a trigger/event happening that causes a workflow to run and to use or modify K8s objects in response to the workflow. 

Events can come from a variety of sources including webhooks, s3, schedules, messaging queues, gcp pubsub, sns, sqs — and by now I am lost between what these short forms mean. The point being, you can set-up a lot of different events to trigger a workflow.

Here are some of the features that are supported by Argo Events

- Supports events from 20+ event sources.
- Ability to customize business-level constraint logic for workflow automation.
- Manage everything from simple, linear, real-time to complex, multi-source events.
- Supports Kubernetes Objects, Argo Workflow, AWS Lambda, Serverless, etc. as triggers.
- [CloudEvents](https://cloudevents.io/) compliant.

Once the event has been registered, it can then trigger either or multiple of the following:

1. Argo Workflows
2. Standard K8s Objects
3. HTTP Requests / Serverless Workloads (OpenFaas, Kubeless, KNative etc.)
4. AWS Lambda
5. NATS Messages
6. Kafka Messages
7. Slack Notifications
8. Argo Rollouts
9. Custom Trigger / Build Your Own Trigger
10. Apache OpenWhisk

Overall, there are 22 different events that can trigger 11 different actions.

**Event Source**

Listens to an event insides and/or outside of the cluster and once an event has been recorded, it will send it to an eventbus.

**Sensor**

The sensor listens to the event bus for certain events and conditional triggers actions.

**Trigger**

What will ultimately happen - the action e.g. our Argo workflow


The eventbus acts as the transport layer of Argo-Events by connecting the event-sources and sensors.

Event-Sources publish the events while the sensors subscribe to the events to execute triggers.

The current implementation of the eventbus is powered by NATS streaming.

## Argo Workflows

Documentation [https://argoproj.github.io/argo-workflows/](https://argoproj.github.io/argo-workflows/)

You would not want to use Argo Events solely by itself because by itself it does not provide a lot of functionality. Instead, you want to set-up Argo events with Argo workflows. 

Argo Workflows is used to orchestrate parallel jobs on Kubernetes. A workflow in itself is a series of steps that follow each other but can also put in parallel. 

In Argo Workflows, each step of the Workflows is its own container. Meaning, you can put anything that you can put into a container image into an Argo Workflow. 

Additionally, Argo Worklfows makes it possible to model multi-step workflows as a sequence of tasks and capture dependencies between tasks using a directed acyclic graph (DAG). Meaning, you can have different steps being dependent on one another and tell Argo Worklfows the order of steps.

Making it possible to run CI/CD pipelines natively on Kubernetes is definitely a big plus since that means that everything that we set-up will be placed directly into Kubernetes.

## Installation

The installation of Argo events is similar to all other Argo projects. Namely, we will start out by 

1. Creating a namespace
2. Installing the CRDs and controllers that the specific Argo tool depends on e.g. Argo events 
3. Argo events requires a specific eventbus to be installed 

First, we want to set-up Argo Workflows by following the getting started guide. 

Note that Argo workflows does not work on all clusters. You can try it out on different clusters but you might need a cluster of a cloud provider.

Next, we want to create a namespace for Argo Workflows that we can use to set-up our 

```jsx
kubectl create ns argo
kubectl apply -n argo -f https://raw.githubusercontent.com/argoproj/argo-workflows/stable/manifests/quick-start-postgres.yaml
```

You can then access the Argo Workflows UI though the following command on localhost:2746

```jsx
kubectl -n argo port-forward deployment/argo-server 2746:2746
```

Then we are going to go ahead and set-up Argo events — we are going to go with the cluster wide installation but you can also choose the namespace specific installation. Please have a look at the documentation for that.

```jsx
kubectl create namespace argo-events
kubectl apply -f https://raw.githubusercontent.com/argoproj/argo-events/stable/manifests/install.yaml
```

Then, we will need the eventbus

```jsx
kubectl apply -n argo-events -f https://raw.githubusercontent.com/argoproj/argo-events/stable/examples/eventbus/native.yaml
```

Before you move to the next step, please make sure to have all the eventbus related pods running in your argo-events namespace

```jsx
kubectl get all -n argo-events
```

Now we will set-up an event source for webhooks. Note that you can also set-up other event sources

```jsx
kubectl apply -n argo-events -f https://raw.githubusercontent.com/argoproj/argo-events/stable/examples/event-sources/webhook.yaml
```

Here is the webhook YAML [https://raw.githubusercontent.com/argoproj/argo-events/stable/examples/event-sources/webhook.yaml](https://raw.githubusercontent.com/argoproj/argo-events/stable/examples/event-sources/webhook.yaml) 

Now create a sensor for the webhook

```jsx
kubectl apply -n argo-events -f https://raw.githubusercontent.com/argoproj/argo-events/stable/examples/sensors/webhook.yaml
```

Have a look at the way the sensor defines the workflow here [https://raw.githubusercontent.com/argoproj/argo-events/stable/examples/sensors/webhook.yaml](https://raw.githubusercontent.com/argoproj/argo-events/stable/examples/sensors/webhook.yaml)

Access the event-source pod via port forward to consumer requests over HTTP

```jsx
kubectl -n argo-events port-forward $(kubectl -n argo-events get pod -l eventsource-name=webhook -o name) 12000:12000 &
```

Now send a request to localhost:12000

```jsx
curl -d '{"message":"this is my first webhook"}' -H "Content-Type: application/json" -X POST http://localhost:12000/example
```

And verify that an Argo workflows was triggered

```jsx
kubectl -n argo-events get workflows | grep "webhook"
```

```jsx
kubectl -n argo-events get wf
```

## ArgoCD with Kustomize

Let's use ArgoCD with Kustomize. 

Since I have created my previous video on ArgoCD, I got several more questions on ArgoCD, specifically, how you can use Kustomize with ArgoCD or ArgoCD not working properly with Kustomize. So let's make ArgoCD play nice with Kustomize.

If you are completely new to ArgoCD and/or Kustomize, I highly suggest you to watch the videos linked above. However, if you are completely new to GitOps, have a look at this blog post which provides a great overview: [https://thenewstack.io/understanding-gitops-the-latest-tools-and-philosophies/](https://thenewstack.io/understanding-gitops-the-latest-tools-and-philosophies/)

This tutorial is going to walk you through:

- Setting up ArgoCD
- Setting up an example project with Kustomize
- Deploying Kustomize YAML files with ArgoCD and allowing ArgoCD to manage the state of those

Let's get started.

## Setting up ArgoCD

First, we are going to need a cluster on which we can run ArgoCD. In my case, I am going to use [Civo Cloud](http://civo.com). 

With every new signup, you will receive $250 worth of credit to spend on Civo Cloud Resources. 

You can find a guide on spinning up a Civo Cloud Kubernetes Cluster here: [https://www.civo.com/learn/creating-a-cluster-the-path-to-success](https://www.civo.com/learn/creating-a-cluster-the-path-to-success)

Alternatively, you can use a KinD Cluster and Spin ArgoCD up there. 

**Spinning up a KinD cluster can be done through the following commands:**

```yaml
brew upgrade
brew install kind
brew install kubectl

kind create cluster --name argocd
```

If you do not have brew installed locally, you can have a look at their [documentation](https://kind.sigs.k8s.io/docs/user/quick-start/) for alternative options. 

Once we have our Kubernetes cluster, whether that is a Civo Cloud cluster or another type of Kubernetes cluster, we can go ahead and install the argocd CLI as well as the ArgoCD CRDs. 

Just a quick recap, ArgoCD is based on Custom Resource Definitions (CRDs for short) which are themselves built on top of Kubernetes and are Kubernetes resources types themselves. 

**Installing the ArgoCD CLI**

```yaml
brew install argocd
```

Alternative installation options are listed here: [https://argoproj.github.io/argo-cd/cli_installation/](https://argoproj.github.io/argo-cd/cli_installation/)

**Installing ArgoCD**

```yaml
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Make sure that all of our resources are set up correctly

```yaml
kubectl get all -n argocd
```

Next, we can access the ArgoCD API Server through the following commands. First, we have to make the service into a type Loadbalancer 

```yaml
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```

Tbh if you are using this in production, you will probably want to use an Ingress resource instead.

Then we can port-forward:

```yaml
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Now the login credentials require you to have access to the ArgoCD secret with the password. You can look up the password through the following command:

```yaml
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

Note that depending on the ArgoCD version that you are using, this command might change. So if it does not work, please take a look at the documentation first before getting frustrated (yes, I have been there and the answer was in the documentation).

Now once we have the password, we can log into ArgoCD:

either through the UI with username=admin and our password from the secret or 

through the CLI through running 

```yaml
argocd login localhost:8080
```

Note that you will only be able to run the command above if your are port-forwarding.

Awesome! At this point, you should have access to the ArgoCD resources running within your cluster. Next, we can setup our Kustomize project. 

## Kustomize

Let's quickly recap what Kustomize is all about, why and how do we use it. 

Kustomize allows you to configure your application configuration. Meaning, instead of having static YAML manifests that you have to manually update, you can have YAML files with the fields specified that should change per deployment. This will allow you to update those YAML files automatically through your CI/CD pipeline before deploying your updated resources.

> Kustomize traverses a Kubernetes manifest to add, remove or update configuration options without forking — Source: [https://kustomize.io/](https://kustomize.io/)


### Installation

You can either go ahead and install the Kustomize CLI or install the kuberctl plugin.

In our case, we are going to use the Kustomize CLI. You can find all installation options here: [https://kubectl.docs.kubernetes.io/installation/kustomize/](https://kubectl.docs.kubernetes.io/installation/kustomize/)

```yaml
brew install kustomize
```

Note that you can also use brew on WSL with brew for Linux: [https://docs.brew.sh/Homebrew-on-Linux](https://docs.brew.sh/Homebrew-on-Linux). This can make your life a lot easier in the long-term. 

### Kustomize example

We are going to use the following example resources to demonstrate the use and value of Kustomize: [https://github.com/AnaisUrlichs/react-article-display](https://github.com/AnaisUrlichs/react-article-display)

Go ahead and clone the repository.

```yaml
git clone git@github.com:AnaisUrlichs/react-article-display.git
```

Next, checkout the branch called Kustomize

```yaml
cd react-article-display

git checkout kustomize
```

Within the branch, you will find a directory called: manifests

The manifests directory contains a kustomization.yaml file that will update the tag of our container image used in the deployment.yaml file. We are following this section of the Kustomize documentation for this: [https://kubectl.docs.kubernetes.io/references/kustomize/images/](https://kubectl.docs.kubernetes.io/references/kustomize/images/)

Note that every directory that contains files that you want to modify needs a kustomization.yaml file. In this case, we just want to modify the deployment.yaml file in ONE directory.

Our kustomization.yaml file:

```jsx
# kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

images:
- name: anaisurlichs/react-example-app
  newTag: 2.0.0

resources:
- deployment.yaml
- service.yaml
```

Next, let's go ahead an create our new manifest with the tag change: 

```yaml
kustomize build ./manifests > ./kustomization/manifests.yaml
```

Once we run the command, we can see that the manifests/manifests.yaml contains our Service and our Deployment YAML but the deployment has a different tag to our original deployment.yaml within the manifests repository.

Note that you can use the same resources from the directory and the same process on your own deployments and container image. You do not have to use this specific example with my tag.

This demo is used merely to demonstrate the use of Kustomize and the process to deploying Kustomize resources through ArgoCD.

Next, we want to go ahead and provide these resources to ArgoCD which is running within our cluster so that it can deploy those.

## Deploying Kustomize resources to ArgoCD

Again, make sure that you are connected to your Kubernetes cluster that we set up at the beginning and logged into ArgoCD.

Our example will live in the kustomize namespace. Let's go ahead and create the namespace in our cluster:

```yaml
kubectl create ns kustomize
```

I will show you two ways to deploy your Kustomize file. The first time, we are going to use the CLI and reference our GitHub repository — specifically, the kustomize branch. 

```yaml
argocd app create react-app --repo https://github.com/AnaisUrlichs/react-article-display.git --revision kustomize --path ./kustomization --dest-server htt
ps://kubernetes.default.svc --dest-namespace kustomize
```

Next, we want to sync our deployment that is managed by ArgoCD with the resources specified in our manifests.yaml. For this, go ahead and run:

```
argocd app sync react-app
```

Lastly, we can check the status of our deployment through the following command:

```yaml
argocd app get react-app
```

Like we have seen in previous videos. ArgoCD will continuously monitor our resources in our git repository for potential changes.

Every time you create a new kustomize.yaml file and the kustomize.yaml file changes, ArgoCD will detect those changes and make updates to our deployments.

Note that ArgoCD allows you to mention specific Kustomize tags that you can find under the following documentation: 

[https://argoproj.github.io/argo-cd/user-guide/kustomize/](https://argoproj.github.io/argo-cd/user-guide/kustomize/)

You could set-up specific build options per kustomize build version such as the one below:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-cm
  namespace: argocd
  labels:
    app.kubernetes.io/name: argocd-cm
    app.kubernetes.io/part-of: argocd
data:
    kustomize.buildOptions: --load_restrictor none
    kustomize.buildOptions.v3.9.1: --output /tmp
```

These will be applied when you generate new Kustomize files.

## Additional Resources

This presentation on how Kustomize is used at Lyft [https://youtu.be/ahMIBxufNR0](https://youtu.be/ahMIBxufNR0)