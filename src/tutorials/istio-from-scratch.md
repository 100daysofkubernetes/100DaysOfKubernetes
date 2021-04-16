# Setup Istio from scratch

# 100Days Resources
* [Video by Anais Urlichs](https://youtu.be/-X9babxSxK4)
* Add your blog posts, videos etc. related to the topic here!

# Learning Resources
- Istio cli "istioctl" [https://istio.io/latest/docs/setup/install/istioctl/](https://istio.io/latest/docs/setup/install/istioctl/)
- To use kind and ingress together have a look at this blog post [https://mjpitz.com/blog/2020/10/21/local-ingress-domains-kind/](https://mjpitz.com/blog/2020/10/21/local-ingress-domains-kind/)
- Using the first part of this blog post [https://www.arthurkoziel.com/running-knative-with-istio-in-kind/](https://www.arthurkoziel.com/running-knative-with-istio-in-kind/) (if you want to set-up knative on your kind cluster, be my guest, I also have a whole video on using kind)

    [Day 27: **Knative**](https://www.notion.so/Day-27-Knative-edd3b828562f4ce08f42a47393e3b473)

    However, this is not the focus today.

Have a look at the previous day on Service Mesh

[Day 31: Service Mesh](https://www.notion.so/Day-31-Service-Mesh-60e08e4d07d6484e92e50457f5c6b31a)

# Example Notes
## Service Mesh vs Ingress

We would not want to create a LoadBalancer for all of our Services to access each and every individual Service. This is where Ingress comes in. Ingress allows us to configure the traffic to all of our microservice applications. This way, we can easier manage the traffic.

Now to manage the connection between services, we would want to configure a Service Mesh such as Istio. Istio will then take care of the communication between our microservice applications within our cluster. However, it will not take care of external traffic automatically. Instead, what Istio will do is to use an Istio Ingress Gateway that will allow users to access applications from outside the Kubernetes cluster.

The difference here is using the Kubernetes Ingress Resource vs. the Istio Ingress Resource. Have a look at the following blog post that provides comprehensive detail on the differences:

[https://medium.com/@zhaohuabing/which-one-is-the-right-choice-for-the-ingress-gateway-of-your-service-mesh-21a280d4a29c](https://medium.com/@zhaohuabing/which-one-is-the-right-choice-for-the-ingress-gateway-of-your-service-mesh-21a280d4a29c)

## Installation

**Prerequisites**

- Docker desktop installed [https://www.docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop)
- Kind binary installed so that you can run kind commands [https://kind.sigs.k8s.io/docs/user/quick-start/](https://kind.sigs.k8s.io/docs/user/quick-start/)

**Set-up the kind cluster for using Ingress**

In the previous tutorial on Ingress, we used Docker Desktop as our local cluster. You can also set-up Ingress with a kind cluster.

You can configure your kind cluster through a yaml file. In our case, we want to be able to expose some ports through the cluster, so we have to specify this within the config.

Before we create a kind cluster, save the following configurations in a kind-config.yaml file.

```jsx
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  kubeadmConfigPatches:
  - |
    kind: InitConfiguration
    nodeRegistration:
      kubeletExtraArgs:
        node-labels: "ingress-ready=true"
  extraPortMappings:
  - containerPort: 80
    hostPort: 80
    protocol: TCP
  - containerPort: 443
    hostPort: 443
    protocol: TCP
```

This file is going to be used in the following command when we create out kind cluster.

```jsx
kind create cluster --config kind-config.yaml --name istio-test
```

Now you should be on your cluster istio-test. In your kubectl config context the cluster will be called kind-istio-test.

```jsx
kubectl config get-contexts
kind get clusters
```

You can then follow the documentation on setting up different Ingress Controllers on top 

**Install Istio**

Install Istio with the following commands if you are on Linux, check out other installation options.

```jsx
curl -L https://istio.io/downloadIstio | sh -
```

Now you have to move the package into your usr/bin directory so you can access it through the command line.

```jsx
sudo mv /Downloads/istio-1.9.1/bin/istioctl /usr/local/bin/
```

When you run now the following, you should see the version of istioctl

```jsx
istioctl version
```

Awesome! we are ready to install istio on our kind cluster.

We are going to use the following Istio configurations and then going to use istioctl to install everything on our kind cluster.

```jsx
istioctl install --set profile=demo
```

Note that you can use another istio profile, other than the default one.

Let's check that everything is set-up correctly

```jsx
kubectl get pods -n istio-system
```

You should have two pods running right now.

You can check whether everything is working properly through the following command

```jsx
istioctl manifest generate --set profile=demo | istioctl verify-install -f -
```

By default, Istio will set the Ingress type to Loadbalancer. However, on a local kind cluster, we cannot use Loadbalancer. Instead, we have to use NodePort and set the host configurations. Create a file called patch-ingressgateway-nodeport.yaml with the following content:

```jsx
spec:
  type: NodePort
  ports:
  - name: http2
    nodePort: 32000
    port: 80
    protocol: TCP
    targetPort: 80
```

And then apply the patch with

```jsx
kubectl patch service istio-ingressgateway -n istio-system --patch "$(cat patch-ingressgateway-nodeport.yaml)"
```

However, in our Docker Desktop cluster, we are connected to [localhost](http://localhost) automatically so in that case, we do not have to change anything to NodePort.

Next, we have to allow Istio to communicated with our default namespace:

```jsx
kubectl label namespace default istio-injection=enabled
```

**Set-up Ingress Gateway**

From here onwards, we are just following the documentation

Setting-up the example application:

[https://istio.io/latest/docs/examples/bookinfo/](https://istio.io/latest/docs/examples/bookinfo/)

Setting-up metrics:

[https://istio.io/latest/docs/tasks/observability/metrics/tcp-metrics/](https://istio.io/latest/docs/tasks/observability/metrics/tcp-metrics/)

If you feel ready for a challenge, try out canary updates with Istio

[https://istio.io/latest/docs/setup/upgrade/canary/](https://istio.io/latest/docs/setup/upgrade/canary/)