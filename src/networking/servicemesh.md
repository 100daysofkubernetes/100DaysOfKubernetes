# Service Mesh

# 100Days Resources
- [Video by Anais Urlichs](https://youtu.be/4LU-XaQ7zzA)
- Add your blog posts, videos etc. related to the topic here!

# Learning Resources
- [Microsoft Introduction to Service Mesh](https://docs.microsoft.com/en-us/azure/aks/servicemesh-about)
- [Nginx explanation on Service Mesh](https://www.nginx.com/blog/what-is-a-service-mesh/)

# Example Notes

**The goal is:**

1. Higher Portability: Deploy it wherever
2. Higher Agility: Update whenever 
3. Lower Operational Management: Invest low cognitive 
4. Lower Security Risk

**How do Services find each other?**

- Answering this question allows us to break down the value of Service Mesh — different Services have to find each other.
- If one service fails, the traffic has to be routed to another service so that requests don't fail

Service-discovery can become the biggest bottleneck.

Open platform, independent service mesh.

In its simplest form a service mesh is a network of your microservices, managing the traffic between services. This allows it to manage the different interactions between your microservices.

A lot of the responsibilities that a service mesh has could be managed on an application basis. However, with the service mesh takes that logic out of the application specific services and manages those on an infrastructure basis.

**Why do you need Service Mesh?**

Istio is a popular solution for managing communication between microservices.

When we move from monolithic to microservice application, we run into several issues that we did not have before. It will need the following setup

1. Each microservice has its own business logic — all service endpoints must be configured
2. Ensure Security standards with firewall rules set-up — every service inside the cluster can talk to every other service if we do not have any additional security inside — for more important applications this is not secure enough. This may result in a lot of complicated configuration.

To better manage the application configuration, everything but the business logic could be packed into its own Sidecar Proxy, which would then be responsible to

- Handle the networking logic
- Act as a Proxy
- Take care of third-party applications
- Allow cluster operators to configure everything easily
- Enable developers to focus on the actual business logic

A service mesh will have a control plane that will inject this business logic automatically into every service. Once done, the microservices can talk to each other through proxies.

Core feature of service mesh: **Traffic Splitting:**

- When you spin up a new service in response to a high number of requests, you only want to forward about 10% of the traffic to the new Service to make sure that it really works before distributing the traffic between all services. This may also be referred to as Canary Deployment

"In a service mesh, requests are routed 
between microservices through proxies in their own infrastructure layer.
 For this reason, individual proxies that make up a service mesh are 
sometimes called “sidecars,” since they run *alongside* each service, rather than *within* them. Taken together, these “sidecar” proxies—decoupled from each service—form a mesh network."

![https://www.redhat.com/cms/managed-files/service-mesh-1680.png](https://www.redhat.com/cms/managed-files/service-mesh-1680.png)

"A sidecar proxy sits alongside a microservice and routes requests to 
other proxies. Together, these sidecars form a mesh network."

**Service Mesh is just a paradigm and Istio is one of the implementations**

Istio allows Service A and Service B to communicate to each other. Once your microservices scale, you have more services, the service mesh becomes more complicated — it becomes more complicated to manage the connection between different services. That's where Istio comes in.

It runs on 

- Kubernetes
- Nomad
- Console

I will focus on Kubernetes.

**Features**

- Load Balancing: Receive some assurance of load handling — enabled some level of abstraction that enables services to have their own IP addresses.
- Fine Grain Control: to make sure to have rules, fail-overs, fault connection
- Access Control: Ensure that the policies are correct and enforceable
- Visibility: Logging and graphing
- Security: It manages your TSL certificates

Additionally, Service Mesh makes it easier to discover problems within your microservice architecture that would be impossible to discover without.

**Components** — Connect to the Control  Plane API within Kubernetes — note that this is the logic of Istio up to version 1.5. The latest versions only deal with Istiod.

1. Pilot: Has A/B testing, has the intelligence how everything works, the driver of Istio
2. Cit: Allows Service A and Service B to talk to each other

**How do we configure Istio?**

1. You do not have to modify any Kubernetes Deployment and Service YAML files
2. Istio is configured separately from application configuration
3. Since Istio is implemented through Kubernetes Custom Resource Definitions (CRD), it can be easily extended with other Kubernetes-based plug-ins
4. It can be used like any other Kubernetes object

The Istio-Ingress Gateway is an entry-point to our Kubernetes cluster. It runs as a pod in our cluster and acts as a LoadBalancer.

## Service Mesh Interface

With different projects and companies creating their own Service Mesh, the need for standards and specifications arise. One of those standards is provided by the Service Mesh Interface (SMI). In its most basic form, SMI provides a list of Service Mesh APIs. Separately SMI is currently a CNCF sandbox project.

SMI provides a standard interface for Service Mesh on Kubernetes

- Provides a basic set of features for the most common use cases
- Flexible to support new use case over time

Website with more information

**SMI covers the following**

- Traffic policy – apply policies like identity and transport encryption across services
- Traffic telemetry – capture key metrics like error rate and latency between services
- Traffic management – shift traffic between different services

## Other Service Mesh Examples

1. Gloo Mesh: Enterprise version of Istio Service Mesh but also has a Gloo Mesh open source version.
2. [Linkerd](https://linkerd.io/): Its main advantage is that it is lighter than Istio itself. Note that Linkerd was origially developed by Buoyant. Linkerd specifically, is run through an [open governance model.](https://linkerd.io/2019/10/03/linkerds-commitment-to-open-governance/)
3. [Nginx service mesh](https://www.nginx.com/products/nginx-service-mesh/): Focused on the data plane and security policies; platform agnostic; traffic orchestration and management