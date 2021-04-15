# Kubernetes Operators

# 100Days Resources
* [Video by Anais Urlichs](https://youtu.be/C_mlWhSrqEY)
* Add your blog posts, videos etc. related to the topic here!

# Learning Resources
- [https://youtu.be/ha3LjlD6g7g](https://youtu.be/ha3LjlD6g7g)
- [https://www.redhat.com/en/topics/containers/what-is-a-kubernetes-operator](https://www.redhat.com/en/topics/containers/what-is-a-kubernetes-operator)
- [https://kubernetes.io/docs/concepts/extend-kubernetes/operator/](https://kubernetes.io/docs/concepts/extend-kubernetes/operator/)

# Example Notes

There is so much great content around Kubernetes Operators, and I have mentioned it several times across the previous videos. However, we have not looked at Kubernetes Operators yet. Today, we are going to explore 

- What are Kubernetes Operators
- How do Operators work
- Why are they important

Operators are mainly used for Stateful Applications

Operators are software extensions to Kubernetes that make use of custom resources to manage applications and their components. Operators follow Kubernetes principles, notably the control loop — [Source](https://kubernetes.io/docs/concepts/extend-kubernetes/operator/)

## Managing Stateful Applications without an operator vs. with an operator.

If you are deploying an application, you usually use 

- Service
- Deployment
- ConfigMap

Kubernetes knows what the desired state of our cluster is because we told it through our configuration files. It aims to match the actual state of our cluster to our desired state.

Now for a stateful application the process is a bit more difficult — it does not allow Kubernetes to automatically scale etc. 

E.g. SQL databases are not identical replicas. There has to be a constant communication for the data to be consistent + other factors.

Each database has its own workaround —> this makes it difficult to use Kubernetes to automate any workaround.

A lot of times stateful applications will require manual intervention i.e. human operators. However, having to manually update resources in Kubernetes goes against its 

So there is a need for an alternative to manage stateful applications. This alternative is a Kubernetes Operator. 

## What is an Operator?

Replace all the manual tasks that the human operator would do. It takes care of

- Deploying the app
- Creating replicas
- Ensuring recovery in case of failure

With this, an Operator is basically an application-specific controller that extends the functionality of the Kubernetes API to take care of the management of complex applications. This is making tasks automated and reusable.

**Operators rely on a control loop mechanism.**

If one replica of our database dies, it will create a new one. If the image version of the database get updated, it will deploy the new version.

Additionally, Operators rely on Kubernetes Custom Resource Definitions (CRDs). CRDs are custom resources created on top of Kubernetes. CRDs allow Operators to have specific knowledge of the application that they are supposed to manage.

You can find a list of Kubernetes Operators in the Kubernetes Operator Hub [https://operatorhub.io/](https://operatorhub.io/)

AND you can find several awesome operators in the wild [https://github.com/operator-framework/awesome-operators](https://github.com/operator-framework/awesome-operators) 😄

Once you have created an operator, it will take high-level inputs and translate them into low level actions and tasks that are needed to manage the application.

Once the application is deployed, the Operator will continue to monitor the application to ensure that it is running smoothly.

## **Who is creating Operators?**

Those if the insights and know-how of the application that the operator is supposed to run.

There is an Operator Framework that basically provides the building blocks that can be used to

The Operator Framework includes:

- **Operator SDK:** Enables developers to build operators based on their expertise without requiring knowledge of Kubernetes API complexities.
- **Operator Lifecycle Management:** Oversees installation, updates, and management of the lifecycle of all of the operators running across a Kubernetes cluster.
- **Operator Metering:** Enables usage reporting for operators that provide specialized services.

Some of the things that you can use an operator to automate include:

- deploying an application on demand
- taking and restoring backups of that application's state
- handling upgrades of the application code alongside related changes such
as database schemas or extra configuration settings
- publishing a Service to applications that don't support Kubernetes APIs to
discover them
- simulating failure in all or part of your cluster to test its resilience
- choosing a leader for a distributed application without an internal
member election process

## Practical Example

In the case of Prometheus, I would have to deploy several separate components to get Prometheus up and running, which is quite complex + deploying everything in the right order.

