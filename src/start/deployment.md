# Kubernetes Deployments 

# 100Days Resources
* [Video by Anais Urlichs](https://youtu.be/qt76R2G4h-0)
* Add your blog posts, videos etc. related to the topic here!

# Learning Resources
- [Kubernetes Deployments official documentation](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)

# Example Notes

This little exercise will be based on the following application: [https://github.com/anais-codefresh/react-article-display](https://github.com/anais-codefresh/react-article-display)

Then we will create a deployment

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
```

More information on Kubernetes deployments

- A deployment is a Kubernetes object that makes it possible to manage multiple, identical pods
- Using deployments, it is possible to automate the process of creating, modifying and deleting pods — it basically manages the lifecycle of your application
- Whenever a new object is created, Kubernetes will ensure that this object exist
- If you try to set-up pods manually, it can lead to human error, using deployments
- The difference between a deployment and a service is that a deployment ensures that a set of pods keeps running by creating pods and replacing broken prods with the resource defined in the template. In comparison, a service is used to allow a network to access the running pods.

[Deployments allow you to](https://www.redhat.com/en/topics/containers/what-is-kubernetes-deployment)

- Deploy a replica set or pod
- Update pods and replica sets
- Rollback to previous deployment versions
- Scale a deployment
- Pause or continue a deployment

Create deployment

```jsx
kubectl create -f deployment.yaml
```

Access more information on the deployment 

```jsx
kubectl describe deployment <deployment name>
```

Create the service yml

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

Creating the service with kubectl expose

```jsx
kubectl expose deployment/my-nginx
```

This will create a service that is highly similar to our in yaml defined service. However, if we want to create the service based on our yaml instead, we can run:

```jsx
kubectl create -f my-pod-service.yml
```

### How is the Service and the Deployment linked?

The targetPort in the service yaml links to the container port in the deployment. Thus, both have to be, for example, 80. 

We can then create the deployment and service based on the yaml, when you look for "kubectl get service", you will see the created service including the Cluster-IP. Take that cluster IP and the port that you have defined in the service e.g. 10.152.183.79:8080 basically <Cluster IP>:<port defined in service> and you should be able to access the application through NodePort. However, note that anyone will be able to access this connection. You should be deleting these resources afterwards.

```jsx
kubectl get service
```

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3f2ba295-c81d-4836-8b46-eadbc70b2eb7/Screenshot_from_2021-01-06_21-54-40.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3f2ba295-c81d-4836-8b46-eadbc70b2eb7/Screenshot_from_2021-01-06_21-54-40.png)

Alternatively, for more information of the service

```jsx
kubectl get svc <service name> -o yaml
```

-o yaml: the data should be displayed in yaml format

Delete the resources by

```jsx
kubectl delete service react-application
kubectl delete deployment react-application

// in this case, your pods are still running, so you would have to remove them individually 
```

Note: replace react-application with the name of your service.