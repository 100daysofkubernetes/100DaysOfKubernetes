# Ingress

# 100Days Resources
* [Video by Anais Urlichs](https://youtu.be/rNvFvGVzT5o)
* Add your blog posts, videos etc. related to the topic here!

# Learning Resources
 
TODO

# Example Notes

Ingress is responsible for managing the external access to our cluster. Whereby it manages

- forwarding rules based on paths and domains
- SSl termination
- and several other features.

The API provided by Ingress allows us to replace an external proxy with a loadbalancer.

Things we want to resolve using Ingress

- Not having to use a fixed port — if we have to manage multiple clusters, we would have a hard time managing all those ports
- We need standard HTTPS(443) or HTTP (80) ports through a predefined path

When we open an application, the request to the application is first received by the service and LoadBalancer, which is the responsible for forwarding the request to either of the pods it is responsible for.

To make our application more secure, we need a place to store the application's HTTPS certificate and forwarding. Once this is implemented, we have a mechanism that accepts requests on specific ports and forwards them to our Kubernetes Service.

The Ingress Controller can be used for this. 

Unlike other Kubernetes Controllers, it is not part of our cluster by default but we have to install it separately.

If you are using minikube, you can check the available addons through 

```jsx
minikube addons list
```

And enable ingress (in case it is not enabled) 

```jsx
minikube addons enable ingress
```

If you are on microk8s, you can enable ingress through

```jsx
microk8s enable ingress
```

You can check whether it is running through — if the pods are running for nginx-ingress, they will be listed

```jsx
kubectl get pods --all-namespaces | grep nginx-ingress
```

If you receive an empty output, you might have to wait a little bit longer for Ingress to start.

Here is the YAML definition of our Ingress resource

```jsx
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: react-application
  annotations:
    kubernetes.io/ingress.class: "nginx"
    ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
spec:
  rules:
  - http:
      paths:
      - path: /demo
        backend:
          serviceName: react-application
          servicePort: 8080
```

- The annotation section is used to provide additional information to the Ingress controller.
- The path is the path after the

You can find a list of annotations and the controllers that support them on this page: [https://github.com/kubernetes/ingress-nginx/blob/master/docs/user-guide/nginx-configuration/annotations.md](https://github.com/kubernetes/ingress-nginx/blob/master/docs/user-guide/nginx-configuration/annotations.md)

We have to set the ssl redirect to false since we do not have an ssl certificate.

You can create the resource through

```jsx
kubectl create \
-f <name of your file>
```

If your application's service is set to NodePort, you will want to change it back into ClusterIP since there is no need anymore for NodePort.

What happens when we create a new Ingress resource?

1. kubectl will send a request to the API Server of our cluster requesting the creation of a new Ingress resource
2. The ingress controller is consistently checking the cluster to see if there is a new ingress resource
3. Once it sees that there is a new ingress resource, it will configure its loadbalancer 

Ingress is a kind of service that runs on all nodes within your cluster. As long as requests match any of the rules defined within Ingress, Ingress will forward the request to the respective service.

To view the ingress running inside your cluster, use

```jsx
kubectl get ing
```

Note that it might not work properly on microk8s.