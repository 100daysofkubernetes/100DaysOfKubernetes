# Kustomize

# 100Days Resources
* [Video by Anais Urlichs](https://youtu.be/Gc-nO3oxxk8)
* Add your blog posts, videos etc. related to the topic here!

# Learning Resources
- Their [webiste](https://kustomize.io/) has lots of amazing videos

# Example Notes

Configuration Management for Kubernetes

— "A template free way to customize application configuration that simplifies the use of off-the-shelf applications"

When I create a YAML, the manifests go into the API server of the main node; the cluster then aims to create the resources within the cluster to match the desired state defined in the YAML

Different to what we have seen before in the videos, YAML can get super complex! Additionally, there are several aspects of the state of our deployment that we want to frequently change. Including:

- Namespaces, Labels, Container Registry, Tags and more

Then, we have resources and processes that we want to change a bit less frequently, such as

- Management Parameters
- Environment-specific processes and resources
- Infrastructure mapping

Kustomize allows you to specify different values of your Kubernetes resources for different situations. 

To make your YAML resources more dynamic and to apply variations between environments, you can use Kustomize.

## Let's get started using Kustomize

Install Kustomize — just reading about it is not going to help us.

[Here is their official documentation](https://kubectl.docs.kubernetes.io/installation/) 

However, their options did not work for the Linux installation, which I also need on WSL — this one worked:

[https://weaveworks-gitops.awsworkshop.io/20_weaveworks_prerequisites/15_install_kustomize.html](https://weaveworks-gitops.awsworkshop.io/20_weaveworks_prerequisites/15_install_kustomize.html) 

Kustomize is part of kubectl so it should work without additional installation using 'kubectl -k' to specify that you want to use kustomize.

Next, scrolling through their documentation, they provide some amazing resources with examples on how to use kubectl correctly — but I am looking for kustomize example

Have a look at their guides if you are curious [https://kubectl.docs.kubernetes.io/guides/](https://kubectl.docs.kubernetes.io/guides/) 

So with kustomize, we want to have our YAML tempalte and then customize the values provided to that resource manifest. However, each directory that is referenced within kustomized must have its own kustomization.yaml file.

First, let's set-up a deployment and a service, like we did in one of the previous days.

The Deployment:

```jsx
apiVersion: apps/v1
kind: Deployment
metadata:
  name: react-application
spec:
  replicas: 2
  selector:
    matchLabels:
      run: react-application
  template:
    metadata:
      labels:
        run: react-application
    spec:
      containers:
      - name: react-application
        image: anaisurlichs/react-article-display:master
        ports:
          - containerPort: 80
        imagePullPolicy: Always
				env:
            - name: CUSTOM_ENV_VARIABLE
              value: Value defined by Kustomize ❤️
```

The service

```jsx
apiVersion: v1
kind: Service
metadata:
  name: react-application
  labels:
    run: react-application
spec:
  type: NodePort
  ports:
  - port: 8080
    targetPort: 80
    protocol: TCP
    name: http
  selector:
    run: react-application
```

Now we want to customize that deployment with specific values.

Set-up a file called 'kustomization.yaml'

```jsx
**resources:
  - deployment.yaml
  - service.yaml**
```

Within this file, we will specify specific values that we will use within our Deployment resource. From a Kubernetes perspective, this is just another Kuberentes resource.

One thing worth mentioning here is that Kustomize allows us to combine manifests from different repositories.

So once you want to apply the kustomize resources, you can have a look at the changed resources:

```jsx
kustomize build .
```

this will give you the changed YAML files with whatever you had defined in your resources.

Now you can forward those resources into another file:

```jsx
kustomize build . > mydeployment.yaml
```

Now if you have kubectl running, you could specify the resource that you want to use through :

```jsx
kubectl create -k .
```

-k refers here to - -kustomize, you could use that flag instead if you wanted to.

This will create our Deployment and service — basically all of the resources that have been defined in your kustomization.yaml file

```jsx
kubectl get pods -l <label specifying your resources>
```

A different kustomization.yaml

Use this within one environment

```jsx
resources:
- ../../base
namePrefix: dev-
namespace: development
commonLabels:
  environment: development

```

And this file for the other environment

```jsx
resources:
- ../../base
namePrefix: prod-
namespace: production
commonLabels:
  environment: production
  sre-team: blue
```

Create the new file that includes kustomize and the original resources

```jsx
kustomize build . > mydeployment.yaml
```

Check-out the video for more 🙂

## Resources

For a comprehensive overview: [https://www.youtube.com/watch?v=Twtbg6LFnAg&t=137s](https://www.youtube.com/watch?v=Twtbg6LFnAg&t=137s)

And another hands-on walkthrough: [https://youtu.be/ASK6p2r-Yrk](https://youtu.be/ASK6p2r-Yrk)

A really good blog post on Kustomize: [https://blog.scottlowe.org/2019/09/13/an-introduction-to-kustomize/](https://blog.scottlowe.org/2019/09/13/an-introduction-to-kustomize/)