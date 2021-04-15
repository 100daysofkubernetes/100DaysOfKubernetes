# Ingress from scratch

# 100Days Resources
* [Video by Anais Urlichs](https://youtu.be/13y1tWEK2ZY)
* Add your blog posts, videos etc. related to the topic here!

# Learning Resources
TODO

# Example Notes

If you are new to 100 Days Of Kubernetes, have a look at the previous days to fill in the gaps and fundamentals for today:

[Day 4: Looking at Services](https://www.notion.so/Day-4-Looking-at-Services-b0e7a6aa09fc4de39ad107ea133c708f)

[Day 11: More exercises on Services](https://www.notion.so/Day-11-More-exercises-on-Services-0f98b7b9620841e8b26ad3f507849683)

[Day 13: Ingress](https://www.notion.so/Day-13-Ingress-89d34d81c5a549aeba6ad10c3d3d9d9c)

[Day 31: Service Mesh](https://www.notion.so/Day-31-Service-Mesh-60e08e4d07d6484e92e50457f5c6b31a)

## Resources

1. Do I need a service mesh or is Ingress enough? [https://www.nginx.com/blog/do-i-need-a-service-mesh/](https://www.nginx.com/blog/do-i-need-a-service-mesh/)
2. Check the specific installation guide [https://kubernetes.github.io/ingress-nginx/deploy/](https://kubernetes.github.io/ingress-nginx/deploy/#microk8s)
3. Tutorial setup [https://medium.com/analytics-vidhya/configuration-of-kubernetes-k8-services-with-nginx-ingress-controller-5e2c5e896582](https://medium.com/analytics-vidhya/configuration-of-kubernetes-k8-services-with-nginx-ingress-controller-5e2c5e896582)
4. Comprehensive Tutorial by Red Hat [https://redhat-scholars.github.io/kubernetes-tutorial/kubernetes-tutorial/ingress.html](https://redhat-scholars.github.io/kubernetes-tutorial/kubernetes-tutorial/ingress.html)
5. Using Ingress on Kind cluster [https://kind.sigs.k8s.io/docs/user/ingress/#ingress-nginx](https://kind.sigs.k8s.io/docs/user/ingress/#ingress-nginx)
6. More on installing ingress on kind [https://dustinspecker.com/posts/test-ingress-in-kind/](https://dustinspecker.com/posts/test-ingress-in-kind/)

## Install

The installation will slightly depend on the type of cluster that you are using. 

For this tutorial, we are going to be using the docker-desktop cluster

We need two main components

1. Ingress Controller
2. Ingress resources

    Note that depending on the Ingress controller that you are using for your Ingress, the Ingress resource that has to be applied to your cluster will be different. In this tutorial, I am using the Ingress Nginx controller.

## Let's set everything up.

We are going to be using the NGINX Ingress Controller. With the NGINX Ingress Controller for Kubernetes, you get basic load balancing, SSL/TLS termination, support for URI rewrites, and upstream SSL/TLS encryption

First off, let's clone this repository: [https://github.com/AnaisUrlichs/ingress-example](https://github.com/AnaisUrlichs/ingress-example)

```jsx
git clone <repository>
```

```jsx
cd ingress-example
```

```jsx
cd app-one
```

now build your Docker image

```jsx
docker build -t anaisurlichs/flask-one:1.0 .
```

You can test it out through

```jsx
docker run -p 8080:8080 anaisurlichs/flask-one:1.0
```

And then we are going to push the image to our Docker Hub

```jsx
docker push anaisurlichs/flask-one:1.0
```

We are going to do the same in our second example application

```jsx
cd ..
cd app-two
```

build the docker image

```jsx
docker build -t anaisurlichs/flask-two:1.0 .
```

push it to your Docker Hub

```jsx
docker push anaisurlichs/flask-two:1.0
```

Now apply the deployment-one.yaml and the deployment-tow.yaml

```jsx
kubectl apply -f deployment-one.yaml
kubectl apply -f deployment-two.yaml
```

Make sure they are running correctly 

```jsx
kubectl get all
```

Installing Ingress Controller

```jsx
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.44.0/deploy/static/provider/cloud/deploy.yaml
```

**Installing Ingress Resource**

```jsx
kubectl apply -f ingress.yaml
```

make sure that everything is running correctly

```jsx
kubectl get all -n ingress-nginx
```

Making paths happen [https://github.com/kubernetes/ingress-nginx/issues/3762](https://github.com/kubernetes/ingress-nginx/issues/3762)