# Prometheus Exporter

# 100Days Resources
* [Video by Anais Urlichs](https://youtu.be/qItg7P1H7uw)
* Add your blog posts, videos etc. related to the topic here!

# Learning Resources
TODO

# Example Notes

## Install Prometheus Helm Chart with Operators etc.

First off, we are going to follow the commands provided in the previous day — Day 28 — on Prometheus to have our Helm Chart with all the Prometheus related resources installed.

[Day 28: What is Prometheus](https://www.notion.so/Day-28-What-is-Prometheus-a05167f73d67446784dc01b8c2e895d8)

## Have a look at the Prometheus Resources to understand those better

Prometheus uses ServiceMonitors to discover endpoints. You can get a list of all the ServiceMonitors through:

```jsx
kubectl get servicemonitor
```

Now have a look at one of those ServiceMonitors:

```jsx
kubectl get servicemonitor prometheus-kube-prometheus-grafana -o yaml
```

This will display the ServiceMonitor definition in pure YAML inside our terminal. Look through the YAML file and you will find a label called:

"release: prometheus"

This label defines the ServiceMonitors that Prometheus is supposed to scrape.

Like mentioned in the Previous video, operators, such as the Prometheus Operator rely on CRD. Have a look at the CRDs that Prometheus uses through:

```jsx
kubectl get crd
```

You can take a look at a specific one as follows.

```jsx
kubectl get prometheus.monitoring.coreos.com -o yalm
```

## Set-up MongoDB

Now, we want to install a MongoDB image on our cluster and tell Prometheus to monitor it's endpoint. However, MongoDB is one of those images that relies on an exporter for its service to be visible to Prometheus. Think about it this way, Prometheus needs the help of and Exporter to know where MongoDB is in our cluster — like a pointer.

You can learn more about those concepts in my previous videos

1. [Prometheus on Kubernetes: Day 28 of #100DaysOfKubernetes](https://youtu.be/n4eF8EcgpZQ)
2. [Kubernetes Operators: Day 29 of #100DaysOfKubernetes](https://youtu.be/C_mlWhSrqEY)

First off, we are going to install the MongoDB deployment and the MongoDB service; here is the YAML needed for this:

deployment.yaml

```jsx
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongodb-deployment
  labels:
    app: mongodb
spec:
  replicas: 2
  selector:
    matchLabels:
      app: mongodb
  template:
    metadata:
      labels:
        app: mongodb
    spec:
      containers:
      - name: mongodb
        image: mongo
        ports:
        - containerPort: 27017
```

service.yaml

```jsx
apiVersion: v1
kind: Service
metadata:
  name: mongodb-service
spec:
  selector:
    app: mongodb
  ports:
  - protocol: TCP
    port: 27017
    targetPort: 27017
```

Use the follow to apply both to your cluster

```jsx
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

You can check that both are up and running through

```jsx
kubectl get all 
# or

kubectl get service
kubectl get pods
```

Now we want to tell Prometheus to monitor that endpoint — for this, we are going to use the following Helm Chart [https://github.com/prometheus-community/helm-charts/tree/main/charts/prometheus-mongodb-exporter](https://github.com/prometheus-community/helm-charts/tree/main/charts/prometheus-mongodb-exporter)

You can find a list of Prometheus exporters and integrations here:

[https://prometheus.io/docs/instrumenting/exporters/](https://prometheus.io/docs/instrumenting/exporters/) 

Next, we are going to add the Helm Mongo DB exporter

```jsx
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
```

```jsx
helm show values rometheus-community/prometheus-mongodb-exporter > values.yaml
```

We need to mondify the values provided in the values.yaml file as follows:

```jsx
mongodb:
  uri: "mongodb://mongodb-service:27017"

serviceMonitor:
  additionalLabels:
    release: prometheus
```

Basically, replace the values.yaml file created in the helm show command above with this file. In this case, we are going to tell the helm chart the mongodb endpoint and then the additional label for the ServiceMonitor.

Next, let's install the Helm Chart and pass in the values.yaml

```jsx
helm install mongodb-exporter prometheus-community/prometheus-mongodb-exporter -f values.yaml
```

You can see whether the chart got installed correctly through

```jsx
helm ls
```

Now have a look at the pods and the services again to see whether everything is running correctly:

```jsx
kubectl get all 
# or

kubectl get service
kubectl get pods
```

Lastly make sure that we have the new ServiceMonitor in our list:

```jsx
kubectl get servicemonitor
```

Have a look at the prometheus label within the ServiceMonitor

```jsx
kubectl get servicemonitor mongodb-exporter-prometheus-mongodb-exporter -o yaml
```

Now access the service of the mongodb-exporter to see whether it scrapes the metrics of our MongoDB properly:

```jsx
kubectl port-forward service/mongodb-exporter-prometheus-mongodb-exporter 9216
```

and open the Prometheus service:

```jsx
kubectl port-forward service/prometheus-kube-prometheus-prometheus 9090
```

On [localhost:9090](http://localhost:9090), go to Status - Targets and you can see all of our endpoints that Prometheus currently knows about.
