# Namespaces

# 100Days Resources
* [Video by Anais Urlichs](https://youtu.be/GD-CweSXsfY)
* Add your blog posts, videos etc. related to the topic here!

# Learning Resources
- [TechWorld with Nana explanation on Namespaces](https://youtu.be/K3jNo4z5Jx8)

# Example Notes

In some cases, you want to divide your resources, provide different access rights to those resources and more. This is largely driven by the fear that something could happen to your precious production resources.

However, with every new cluster, the management complexity will scale — the more clusters you have, the more you have to manage — basically, the resource overhead of many small clusters is higher than of one big one. Think about this in terms of houses, if you have one big house, you have to take care of a lot but having several small houses, you have x the number of everything + they will be affected by different conditions. 

## Practical

Let's get moving, you only learn by doing.

Clone this repository [https://github.com/vfarcic/k8s-specs](https://github.com/vfarcic/k8s-specs) 

```jsx
cd k8s-specs
```

and then, we will use this application

```jsx
cat ns/go-demo-2.yml
```

Then we do a nasty work-around to specify the image tag used in the pod

```jsx
IMG=vfarcic/go-demo-2

TAG=1.0

cat ns/go-demo-2.yml \
| sed -e \
"s@image: $IMG@image: $IMG:$TAG@g" \
| kubectl create -f -
```

When the -f argument is followed with a
dash (-), kubectl uses standard input (stdin) instead of a file.

To confirm that the deployment was successful 

```jsx
kubectl rollout status \
2 deploy go-demo-2-api
```

Which will get us the following output

```jsx
hello, release 1.0!
```

Almost every service are Kubernetes Objects. 

```jsx
kubectl get all
```

Gives us a full list of all the resources that we currently have up and running.

The system-level objects within our cluster are usually not visible, only the objects that we created.

Within the same namespace, we cannot have twice the same object with exactly the same name. However, we can have the same object in two different namespaces. 

Additionally, you could specify within a cluster permissions, quotas, policies, and more — will look at those sometimes later within the challenge.

We can list all of our namespaces through

```jsx
kubectl get ns
```

Create a new namespaces

```jsx
kubectl create namespace testing
```

Note that you could also use 'ns' for 'namespace'

Kubernetes puts all the resources needed to execute Kubernetes commands into the kube-system namespace

```jsx
kubectl --namespace kube-system get all
```

Now that we have a namespace testing, we can use it for new deployments — however, specifying the namespace with each command is annoying. What we can do instead is

```jsx
kubectl config set-context testing \
 --namespace testing \
 --cluster docker-desktop \
 --user docker-desktop
```

In this case, you will have to change the command according to your cluster. The created context uses the same cluster as before — just a different namespace.

You can view the config with the following command

```jsx
kubectl config view
```

Once we have a new context, we can switch to that one

```jsx
kubectl config use-context testing
```

Once done, all of our commands will be automatically executed in the testing namespace.

Now we can deploy the same resource as before but specify a different tag.

```jsx
TAG=2.0

DOM=go-demo-2.com

cat ns/go-demo-2.yml \
| sed -e \
"s@image: $IMG@image: $IMG:$TAG@g" \
| sed -e \
"s@host: $DOM@host: $TAG\.$DOM@g" \
| kubectl create -f -
```

to confirm that the rollout has finished

```jsx
kubectl rollout status \
deployment go-demo-2-api
```

Now we can send requests to the different namespaces 

```jsx
curl -H "Host: 2.0.go-demo-2.com" \
2 "http://$(minikube ip)/demo/hello"
```

### Deleting resources

It can be really annoying to have to delete all objects one by one. What we can do instead is to delete all resources within a namespace at one 

```jsx
kubectl delete ns testing
```

The real magic of namespaces is when we combine those with authorization logic, which we are going to be looking at in later videos.