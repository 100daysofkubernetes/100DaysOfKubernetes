# Linkerd

# 100Days Resources
* [Video by Anais Urlichs](https://youtu.be/4Y6TR-sg0mQ)
* Add your blog posts, videos etc. related to the topic here!

# Learning Resources
- [The Linkerd getting started guide](https://linkerd.io/2.10/getting-started/)
- [Video by Saiyam Pathak](https://youtu.be/QGZO2V_N3ts)
- [Have a look at the intro to Service Mesh](../networking/servicemesh.md)

# Example Notes

If you want to get started with Linkerd, for the most part, just follow their Documentation. :)

In some of the previous videos, we looked as Service Mesh, and specifically at Istio. Since many people struggle to set-up Istio I thought it would be great to take another look at a different Service Mesh. In this case, at Linkerd. 

Note that Istio is still one of the most popular. Thus, it is always good to still know about that but as an alternative, let's also take a look at Linkerd.

**Benefits of Linkerd**

> It works as it is — Saiyam

- First service mesh project that introduced the term
- In production for about 4 years
- Open governance model and hosted by the CNCF
- Super extensible and easy to install add-ons
- Easy to install
- It is a community project
- Kept small, you should spend the least amount of resources

**More information**

Control plane written in Go and data plane written in Rust.

## Installation

LinkerD has a really neat [getting-started documentation](https://linkerd.io/2.10/getting-started/) that you can follow step-by-step.

First, we are going to follow the onboarding documentation. Once we have everything set-up and working, we want to see metrics of our application through Prometheus and Grafana. 

Usually I would copy paste commands to make sure everyone knows the order but since their documentation is pretty straight-forward, I am not going to do that this time.

1. Let's follow the steps detailed in the getting-started documentation [https://linkerd.io/2.10/getting-started/](https://linkerd.io/2.10/getting-started/)

In our case, we are going to try it out on our Docker for Desktop Kubernetes cluster but you could also use another cluster such as KinD.

Small side-note, I absolutely love that they have a checker command to see whether LinkerD can actually be installed on your cluster

```jsx
linkerd check --pre
```

How amazing is that?

And then, they have a separate command to see whether all the resources are ready

```jsx
linkerd check
```

I am already in love with them!

We are going to keep following the guide, because we want to have a fancy dashboard with metrics in the end

```jsx
linkerd viz install | kubectl apply -f -
```

```jsx
linkerd jaeger install | kubectl apply -f -
```

Then we are going to check that everything is ready

```jsx
linkerd check
```

Access the dashboard

```jsx
**linkerd viz dashboard &**
```

this will give you the [localhost](http://localhost) port to a beautiful Dashboard

We are going to use the following GitHub respository to install our application

[https://github.com/AnaisUrlichs/react-article-display](https://github.com/AnaisUrlichs/react-article-display)

After clone, cd into react-article-display

Now you want to have Helm istalled since this application relies on a Helm Chart

```jsx
helm install react-article ./charts/example-chart
```

Once installed, we can port-forward to access our application

```jsx
kubectl por-forward service/react-article-example-chart 5000:80
```

Now we just have to connect our Service Mesh with the application

```jsx
kubectl get deploy -o yaml \
  | linkerd inject - \
  | kubectl apply -f -
```

 Then we can go ahead and run checks again

```jsx
linkerd check --proxy
```

We can see the live stats of our application through

```jsx
linkerd viz stat deploy
```

And see the stream of requests to our services throug

```jsx
linkerd viz tap deploy/web
```

Now let's go ahead and see how we can do traffic splitting for Canary deployments with Linkerd. For this, we are going to follow the documentation [https://linkerd.io/2.10/tasks/canary-release/](https://linkerd.io/2.10/tasks/canary-release/)

Clone the following repository

Apply the first application

```jsx
kubectl apply -f app-one.yaml
```

You can access it through kubectl port forwarding

```jsx
kubectl port-forward service/app-one 3000:80
```

Install Flagger

```jsx
kubectl apply -k github.com/fluxcd/flagger/kustomize/linkerd
```

Deploy the deployment resource

```jsx
kubectl create ns test && \
  kubectl apply -f https://run.linkerd.io/flagger.yml
```

Ensure they are ready

```jsx
kubectl -n test rollout status deploy podinfo
```

Access the service

```jsx
kubectl -n test port-forward svc/podinfo 9898
```

```jsx
kubectl -n test get ev --watch
```