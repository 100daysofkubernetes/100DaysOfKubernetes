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

