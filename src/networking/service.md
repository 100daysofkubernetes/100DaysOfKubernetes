# Kubernetes Service

# 100Days Resources
- Video by Anais Urlichs [One](https://youtu.be/4_felE4hMog) and [Two](https://youtu.be/vxA4IP3K7Z4)
- Add your blog posts, videos etc. related to the topic here!

# Learning Resources
- [Official Documentation](https://kubernetes.io/docs/concepts/services-networking/service/)
- [https://kubernetes.io/docs/reference/kubectl/cheatsheet/](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
- [https://katacoda.com/courses/kubernetes](https://katacoda.com/courses/kubernetes)
- Guides and interactive tutorial within the Kubernetes docs [https://kubernetes.io/docs/tutorials/kubernetes-basics/expose/expose-intro/](https://kubernetes.io/docs/tutorials/kubernetes-basics/expose/expose-intro/)
- Kubernetes by example [https://kubernetesbyexample.com/](https://kubernetesbyexample.com/) created by OpenShift

# Example Notes

Pods are formed, destroyed and never repaired. You would not repair an existing, running pod but rather deploy a new, healthy one.

Controllers, along with the Scheduler inside your Kubernetes cluster are making sure that pods are behaving correctly, they are monitoring the pods. 

So far, only containers within the same pod can talk to each other through localhost. This prevents us from scaling our application. Thus we want to enable communication between pods. This is done with Kubernetes Services.

> Kubernetes Services provide addresses through which associated Pods can be accessed.

A service is usually created on top of an existing deployment.

### Follow along

Clone the following repository: [https://github.com/vfarcic/k8s-specs](https://github.com/vfarcic/k8s-specs)

And prepare our minikube, microk8s or whatever you are using as your local cluster 🙂

```jsx
cd k8s-specs

git pull

minikube start --vm-driver=virtualbox

kubectl config current-context
```

For this exercise, we are going to create the following ReplicaSet, similar to what we have done in the previous video. The definition will look as follows:

```jsx
cat svc/go-demo-2-rs.yml
```

Now create the ReplicaSet:

```jsx
kubectl create -f svc/go-demo-2-rs.yml

// get the state of it
kubectl get -f svc/go-demo-2-rs.yml
```

Before continuing with the next exercises, make sure that both replicas are ready

With the kubectl expose command, we can tell Kubernetes that we want to expose a resource as service in our cluster

```jsx
kubectl expose rs go-demo-2 \
 --name=go-demo-2-svc \
 --target-port=28017 \ // this is the port that the MongoDB interface is listening to
 --type=NodePort
```

We can have by default three different types of Services

**ClusterIP**

ClusterIP is used by default. It exposes the service only within the cluster. By default you want to be using ClusterIP since that prevents any external communication and makes your cluster more secure. 

**NodePort** 

Allows the outside world to access the node IP

**and LoadBalancer**

The LoadBalancer is only useful when it is combined with the LoadBalancer of your cloud provider.

The process when creating a new Service is something like this:

1. First, we tell our API server within the master node in our cluster it should create a new service — in our case, this is done through kubectl commands.
2. Within our cluster, inside the master node, we have an endpoint controller. This controller will watch our API server to see whether we want to create a new Service. Once it knows that we want to create a new API server, it will create an endpoint object.
3. The kube-proxy watches the cluster for services and enpoints that it can use to configure the access to our cluster. It will then make a new entry in its iptable that takes note of the new information.
4. The Kube-DNS realises that there is a new service and will add the db’s record to the dns server (skydns).

Taking a look at our newly created service:

```jsx
kubectl describe svc go-demo-2-svc
```

- All the pods in the cluster can access the targetPort
- The NodePort automatically creates the clusterIP
- Note that if you have multiple ports defined within a service, you have to name those ports

Let’s see whether the Service indeed works.
PORT=$(kubectl get svc go-demo-2-svc \
-o jsonpath="{.spec.ports[0].nodePort}")

IP=$(minikube ip)

 open "http://$IP:$PORT"

### Creating Services in a Declarative format

```jsx
cat svc/go-demo-2-svc.yml
```

- The service is of type NodePort - making it available within the cluster
- TCP is used as default protocol
- The selector is used by the service to know which pods should receive requests (this works the same way as the selector within the ReplicaSet)

With the following command, we create the service and then get the sevrice

```jsx
kubectl create -f svc/go-demo-2-svc.yml

kubectl get -f svc/go-demo-2-svc.yml
```

We can look at our endpoint through

```jsx
kubectl get ep go-demo-2 -o yaml
```

- The subset responds to two pods, each pod has its own IP address
- Requests are distributed between these two nodes

Make sure to delete the Service and ReplicaSet at the end

```jsx

kubectl delete -f svc/go-demo-2-svc.yml

kubectl delete -f svc/go-demo-2-rs.yml
```