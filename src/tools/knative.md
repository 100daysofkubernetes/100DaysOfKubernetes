# Knative 

# 100Days Resources
* [Video by Anais Urlichs](https://youtu.be/rZKaju9h7fQ)
* Add your blog posts, videos etc. related to the topic here!

# Learning Resources
TODO

# Example Notes

Kubernetes-based platform to deploy and manage modern serverless workloads. Knative's serving component incorporates Istio, which is an open source tool developed by IBM, Google, and ridesharing company Lyft to help manage tiny, container-based software services known as microservices.

Introduce event-driven and serverless capabilities to Kubernetes clusters. Knative combines two interesting concepts Serverless and Kubernetes Container Orchestration. With Kubernetes, developers have to set-up a variety of different tools to make everything work together, this is time consuming and difficult for many. Knative wants to bring the focus back on writing code instead of managing infrastructure.

Knative allows us to make it super easy to deploy long-running stateless application on top of Kubernetes.

**What is Knative?**

A Kubernetes extension consistent of custom controllers and custom resource definitions that enable new use cases on top of Kubernetes. 

A platform installed on top of Kubernetes that brings serverless capabilities to Kubernetes — with its additional features, it makes it super easy to go serverless on top of Kubernetes.

**The Goal:** Making microservice deployments on Kubernetes really easy.+

Serverless style user experience that lives on top of Kubernetes.

**It consists of three major components:**

1. Note that this part has been deprecated but you will still find it in a lot of tutorials. Build: Every developer has code — then turn it into a container — either one step or consistent of multiple step. Next, push the image to a cloud registry. These are a lot of steps — Knative can do all of this within the cluster itself, making iterative development possible. 
2. Serve: Knative comes with Istio components, traffic management, automatic scaling. It consists of the Route and the Config — every revision of our service is managed by the config
3. Event: You need triggers that are responded to by the platform itself — it allows you to set-up triggers. Also, you can integrate the eventing with your ci/cd flow to kick off your build and serve stages.

Note that you can use other Kubernetes management tools together with Knative

**Features:**

- Focused API with higher level abstractions for common app use-cases.
- Stand up a scalable, secure, stateless service in seconds.
- Loosely coupled features let you use the pieces you need.
- Pluggable components let you bring your own logging and monitoring, networking, and service mesh.
- Knative is portable: run it anywhere Kubernetes runs, never worry about vendor lock-in.
- Idiomatic developer experience, supporting common patterns such as GitOps, DockerOps, ManualOps.
- Knative can be used with common tools and	frameworks such as Django, Ruby on Rails, Spring, and many more.

Knative offers several benefits for Kubernetes users wanting to take their use of containers to the next level:

- **Faster iterative development:** Knative cuts
valuable time out of the container building process, which enables you
to develop and roll out new container versions more quickly. This makes
it easier to develop containers in small, iterative steps, which is a
key tenet of the agile development process.
- **Focus on code:** [DevOps](https://www.ibm.com/cloud/learn/devops-a-complete-guide) may empower developers to administer their own environments, but at the end of the day, coders want to code. You want to focus on building
bug-free software and solving development problems, not on configuring
message bus queues for event triggering or managing container
scalability. Knative enables you to do that.
- **Quick entry to serverless computing:** Serverless
environments can be daunting to set up and manage manually. Knative
allows you to quickly set up serverless workflows. As far as the
developers are concerned, they’re just building a container—it’s Knative that runs it as a service or a serverless function behind the scenes.

**There are two core Knative components that can be installed and used together or independently to provide different functions:**

- [Knative Serving](https://knative.dev/docs/serving/): Provides request-driven compute that can scale to 0. The Serving component is responsible for running/hosting your application.
Easily manage stateless services on Kubernetes by reducing the developer effort required for autoscaling, networking, and rollouts.
- [Knative Eventing](https://knative.dev/docs/eventing/): Management and delivery of events — manage the event infrastructure of your application.
Easily route events between on-cluster and off-cluster components by
exposing event routing as configuration rather than embedded in code.

These components are delivered as Kubernetes custom resource 
definitions (CRDs), which can be configured by a cluster administrator 
to provide default settings for developer-created applications and event
 workflow components.

Additionally, knative keeps track of your revisions.

**Revisions**

1. Revisions of your application are used to scale up the resources once you receive more requests
2. If you are deploying a change/update, revisions can also be used to gradually move traffic from revision 1 to revision 2
3. You can also have revisions that are not part of the networking scheme — in which case, they have a dedicate name and endpoint.

**Prerequisites**

- Kubernetes cluster with v1.17 or newer, note that most have 1.18 already by default but you might want to check
- Kubectl that is connected to your cluster

**The resources that are going to be deployed through Serving**

Knative Serving defines a set of objects as Kubernetes Custom Resource
Definitions (CRDs). These objects are used to define and control how your
serverless workload behaves on the cluster:

- [Service](https://knative.dev/docs/serving/spec/knative-api-specification-1.0#service):
The `service.serving.knative.dev` resource automatically manages the whole
lifecycle of your workload. It controls the creation of other objects to
ensure that your app has a route, a configuration, and a new revision for each
update of the service. Service can be defined to always route traffic to the
latest revision or to a pinned revision.
- [Route](https://knative.dev/docs/serving/spec/knative-api-specification-1.0#route):
The `route.serving.knative.dev` resource maps a network endpoint to one or
more revisions. You can manage the traffic in several ways, including
fractional traffic and named routes.
- [Configuration](https://knative.dev/docs/serving/spec/knative-api-specification-1.0#configuration):
The `configuration.serving.knative.dev` resource maintains the desired state
for your deployment. It provides a clean separation between code and
configuration and follows the Twelve-Factor App methodology. Modifying a
configuration creates a new revision.
- [Revision](https://knative.dev/docs/serving/spec/knative-api-specification-1.0#revision):
The `revision.serving.knative.dev` resource is a point-in-time snapshot of the
code and configuration for each modification made to the workload. Revisions
are immutable objects and can be retained for as long as useful. Knative
Serving Revisions can be automatically scaled up and down according to
incoming traffic. See
[Configuring the Autoscaler](https://knative.dev/docs/serving/autoscaling) for more
information.

![https://github.com/knative/serving/raw/master/docs/spec/images/object_model.png](https://github.com/knative/serving/raw/master/docs/spec/images/object_model.png)

With the Service resource, a deployed service will automatically have a matching route and configuration created. Each time the Service is updated, a new revision is created.

Configuration creates and tracks snapshots of how to run the user's software called Revisions. Since it keeps track of revisions, it allows you to roll back to prevision revisions should you encounter an issue within new deployments.

Data-path/Life of a Request

## Installation

Installing the knative serving component

```jsx
kubectl apply --filename https://github.com/knative/serving/releases/download/v0.20.0/serving-crds.yaml
```

Installing the core serving component

```jsx
kubectl apply --filename https://github.com/knative/serving/releases/download/v0.20.0/serving-core.yaml
```

Installing Istio for Knative [https://knative.dev/docs/install/installing-istio/](https://knative.dev/docs/install/installing-istio/)

Install the Knative Istio controller:

```jsx
kubectl apply --filename https://github.com/knative/net-istio/releases/download/v0.20.0/release.yaml
```

Fetch the External IP or CNAME:

```jsx
kubectl --namespace istio-system get service istio-ingressgateway
```

Issue to delete webhooks

[https://github.com/knative/serving/issues/8323](https://github.com/knative/serving/issues/8323)

**Configure DNS** 

Head over to the docs

[https://knative.dev/docs/install/](https://knative.dev/docs/install/) 

Monitor the Knative components until all of the components show a STATUS of Running or Completed:

```jsx
kubectl get pods --namespace knative-serving
```

You can also use knative with their cli-tool.

Note: Make sure that you have enough resources/capacity of your cluster. If you do receive an error message, increase the capacity of your cluster and rerun commands.

**So the process is now**

1. The client opens the application
2. Which will then forward the request to the Loadbalancer that has been created when we installed Istio (this will only be created on a 'proper cluster')
3. The LoadBalancer will then forward our request to the Istio Gateway — which is responsible for fulfilling the request connected to our application.

**For a stateless application, there should be as a minimum, the following resources:**

1. Deployment
2. ReplicaSet
3. Pod
4. Pod Scalar to ensure the adequate number of pods are running
5. We need a Service so that other Pods/Services can access the application
6. If the application should be used outside of the cluster, we need an Ingress or similar

**So what makes our application serverless?**

When Knative realises that our application is not being used for a while, it will remove the pods needed to run the application ⇒ Scaling the app to 0 Replicas

Knative is a solution for Serverless workloads, it not only scales our application but also queues our requests if there are no pods to handle our requests.

## Resources

- Website [https://knative.dev/](https://knative.dev/)
- Documentation [https://knative.dev/docs/](https://knative.dev/docs/)
- YouTube video [https://youtu.be/28CqZZFdwBY](https://youtu.be/28CqZZFdwBY)

If you set a limit of how many requests one application can serve, you can easier see the scaling functionality of Knative in your cluster.

```jsx
kn service update <nameofservice> --concurrency-limit=1
```