# Prometheus

# 100Days Resources
* [Video by Anais Urlichs](https://youtu.be/n4eF8EcgpZQ)
* Add your blog posts, videos etc. related to the topic here!

# Learning Resources
- Overview video [https://youtu.be/nk3sk1LO7Bo](https://youtu.be/nk3sk1LO7Bo) quick
- Overview video [https://youtu.be/sYMTY-SciUQ](https://youtu.be/sYMTY-SciUQ) long
- The Prometheus documentation
- The Grafana documentation
- Have a look at the different yaml files that encompass Pormetheus [https://phoenixnap.com/kb/prometheus-kubernetes-monitoring](https://phoenixnap.com/kb/prometheus-kubernetes-monitoring)
- Challenges using Prometheus at scale [https://sysdig.com/blog/challenges-scale-prometheus/](https://sysdig.com/blog/challenges-scale-prometheus/)
- Another quick tutorial [https://dev.to/rayandasoriya/kubernetes-monitoring-with-prometheus-2l7k](https://dev.to/rayandasoriya/kubernetes-monitoring-with-prometheus-2l7k)

This is a really useful series by Sysdig

1. [https://sysdig.com/blog/kubernetes-monitoring-prometheus/](https://sysdig.com/blog/kubernetes-monitoring-prometheus/)
2. [https://sysdig.com/blog/kubernetes-monitoring-with-prometheus-alertmanager-grafana-pushgateway-part-2/](https://sysdig.com/blog/kubernetes-monitoring-with-prometheus-alertmanager-grafana-pushgateway-part-2/)
3. [https://sysdig.com/blog/kubernetes-monitoring-prometheus-operator-part3/](https://sysdig.com/blog/kubernetes-monitoring-prometheus-operator-part3/)

# Example Notes

Prometheus is a time-series database that came out of soundcloud to monitor our processes that might span various different systems. It is basically a tool used to monitor the performance of your infrastructure. 

- DevOps needs more automation.
- Want to be notified about storage issues.
- Part of the CNCF landscape

If you have hundred of microservices, you have to have complex monitoring in place. You want to know when your application is not running as expected.

Note that what we want to measure and how we want to measure it highly depends on the type of our application.

Why do we want to use Prometheus for our monitoring on Kubernetes?

1. Key-value pair based — similar to how Kubernetes metadata organises labels
2. "Prometheus is a good fit for microservices because you just need to expose a metrics port, and don’t need to add too much complexity or run additional services. Often, the service itself is already presenting a HTTP interface, and the developer just needs to add an additional path like /metrics." [Source](https://sysdig.com/blog/kubernetes-monitoring-prometheus/)

**At its core component**

- Prometheus server
    - Storage: Time series database
    - Data Retrieval Worker
    - Http server: accepts queries
- Prometheus has an Altertmanager : "The AlertManager receives metrics from the Prometheus server and then is responsible for grouping and making sense of the metrics and then forwarding an alert to your chosen notification system"

## How does it work?

Prometheus operates under the pull-based model. By sending http requests to the endpoint it is supposed to monitor, the target, it scrapes information from that target. The response to this scrape request is stored and parsed in storage along with the metrics for the scrape itself.

Metrics have type and help attributes

- help: what the metrics is
- type: metrics type

Prometheus pulls data from end-points — can easily detect whether a service is up and running e.g. if the end-point does not respond.

Vs. push system — applications and servers are responsible for pushing data — this creates a high traffic load. In this case, you might be overloading your infrastructure. 

If Pormetheus is unable to access the labels of your application, an exporter can be used. There are a lot of different exporters. The exporter you use will depend on the type of application that you want to expose data of.  

Here is one overview from 2018 — the overview provided on the official documentation looks a bit more complex than this one. Note the direction of the arrows.

Source: [https://youtu.be/sYMTY-SciUQ](https://youtu.be/sYMTY-SciUQ)

Additional notes

- You define what targets it is supposed to scape and the interval it should scape at in the yaml file.
- Expects metrics to be available at "/metrics".
- Usually, the yaml file has a default job that can be overwritten with custom jobs.
- Prometheus has something called prompt ql — which is the prometheus query language.
- Grafana is used to pull data out of prometheus.
- By default Prometheus comes with a UI that can be opened on port 9090

Some labels are provided from the application itself and others come directly from the Prometheus server since it knows what it scrapes.

Things can go wrong for a variety of reasons, such as

- **Disk Full** **:** No new data can be store
- **Software Bug :** Request Errors
- **High Temperature :** Hardware Failure
- **Network Outage :** Services cannot communicate
- **Low Memory Utilization :** Money wasted

Note that you can integrate long-term storage with Prometheus but usually if a node fails, the data received until that point will be gone too.

You can expose to Prometheus the following

- ***node***
- ***endpoint***
- ***service***
- ***pod***
- ***ingress***

**Some of the downsides using Prometheus at scale**

- "It doesn’t allow global visibility. Several production clusters would make this a new requirement."
- "Operations can be cumbersome and time consuming."
- "This model can complicate governance and regulatory compliance"
- "Prometheus is not designed to be scaled horizontally. Once you hit the limit of vertical scaling, you’re done."

[Source](https://sysdig.com/blog/challenges-scale-prometheus/)

## Install

To learn more about Prometheus, this repository is really great [https://github.com/yolossn/Prometheus-Basics](https://github.com/yolossn/Prometheus-Basics)

Using Prometheus without Kubernetes on a node.js application

[https://github.com/csantanapr/prometheus-nodejs-tutorial](https://github.com/csantanapr/prometheus-nodejs-tutorial)

Following this tutorial to set-up both Prometheus and Grafana 

[https://grafana.com/docs/grafana-cloud/quickstart/prometheus_operator/](https://grafana.com/docs/grafana-cloud/quickstart/prometheus_operator/)

Prometheus is based on a Kubernetes Operator. Let's take a closer look at Kubernetes Operators. In short, they are used to develop tools and resources on top of Kubernetes. You can use Kubernetes Operators to create new custom resources. 

**Installation**

The Prometheus operator is provided. A deployment, a required ClusterRole with associated ClusterRoleBinding and a ServiceAccount are defined. We can create all of those through the following tutorial.

- Blog post on using Prometheus and Grafana [https://medium.com/devops-dudes/install-prometheus-on-ubuntu-18-04-a51602c6256b](https://medium.com/devops-dudes/install-prometheus-on-ubuntu-18-04-a51602c6256b)

Install the Prometheus operator — I am going to use Helm because it is probably the most straightforward to use vs. any of the other options.

```jsx
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

helm repo update

helm install prom prometheus-community/kube-prometheus-stack
```

To access the Prometheus UI, use the following 

```jsx
kubectl port-forward prometheus-prometheus-prometheus-oper-prometheus-0 9090
```

Note that the prometheus might not be in namespace — if you have installed it in a namespace, make sure to specify the namespace.

Now do the same with the Grafana Dashboard

```jsx
kubectl port-forward prometheus-grafana-5c5885d488-b9mlj 3000
```

Here again, the resource might or might not be in a namespace.

To login, you have to get your password etc.

```jsx
kubectl get secret --namespace prometheus prometheus-grafana -o yaml
```

username: admin

password: prom-operator

Note again that you might have to replace the values.

Now you can access the metrics. Have a look at the following video for a detailed overview:

[https://youtu.be/QoDqxm7ybLc](https://youtu.be/QoDqxm7ybLc)

We want to monitor an application, which we are going to do by following this Readme:

[https://github.com/prometheus-operator/prometheus-operator/blob/master/Documentation/user-guides/getting-started.md](https://github.com/prometheus-operator/prometheus-operator/blob/master/Documentation/user-guides/getting-started.md)

To access the deployment in yaml format

```jsx
kubectl describe deployment prometheus-prometheus-oper-operator > oper.yaml
```

