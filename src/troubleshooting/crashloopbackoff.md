# CrashLoopBackOff

# 100Days Resources
* [Video by Anais Urlichs](https://youtu.be/-ypljeZrSgc)
* Add your blog posts, videos etc. related to the topic here!

# Learning Resources
- What is Kubernetes: [https://youtu.be/VnvRFRk_51k](https://youtu.be/VnvRFRk_51k)
- Kubernetes architecture explained: [https://youtu.be/umXEmn3cMWY](https://youtu.be/umXEmn3cMWY)

# Example Notes

Today I actually spent my time first learning about ReplicaSets. However, when looking at my cluster, I have noticed something strange.

There was one pod still running that I thought I had deleted yesterday.

The status of the pod indicated "CrashLoopBackOff"; meaning the pod would fail every time it was trying to start. It will start, fail, start fail, start f...

This is quite common, and in case the restart policy of the pod is set to always, Kubernetes will try to restart the pod every time it has an error.

**There are several reasons why the pod would end up in this poor state:**

1. Something is wrong within our Kubernetes cluster
2. The pod is configured correctly
3. Something is wrong with the application

In this case, it was easier to identify what had gotten wrong that resulted in this misbehaved pod.

Like mentioned in previous days, there are multiple ways that one can create a pod. This can be categorized roughly into 

- Imperative: We have to tell Kubernetes each step that it has to do within our cluster
- Declarative: We provide Kubernetes with any resource definition that we want to set-up within our pod and it will figure out the steps that are needed to make it happen

As part of yesterdays learning, I tried to set-up a container image inside a pod and inside our cluster with the following command:

*kubectl create deployment --image=<name of the image>*

this will create a deployment resource, based on which a pod is created, based on which a container is run within the created pod.

Going back to reasons this might have resulted in a CrashLoopBackOff; I don't have reasons to believe that something is wrong within our cluster since the same image was created using a pod definition in a declarative format — that worked.

Next, the pod could be configured correctly. This could have been the case — However, since we did not tell Kubernetes explicitly how it should create the pod, we don't have much control over this aspect.

Lastly, the application inside of the container is wrong. I have reason to believe that this is the case. When parsing the container image into the Kubernetes cluster, we did not provide any arguments. However, the container image would have needed an argument to know which image it is actually supposed to run. Thus, I am settling for this explanation.

Now how do we get rid of the pod or correct this?

I first tried to delete the pod with

```jsx
kubectl delete pod <name of the pod>
```

However, this just meant that the current instance of the pod was deleted and a new one created each time — counting the restarts from 0.

So it must be that there is another resource that tells Kubernetes "create this pod and run the container inside"

Let's take a look at the original command:

*kubectl create deployment --image=<name of the image>*

I had literally told Kubernetes to create a deployment. SO let's check for deployment:

```jsx
kubectl get deployment
```

and tada, here is the fish.

Since we have already tried to delete the pod, we will now delete the deployment itself.

```jsx
kubectl delete deployment <name of the deployment>
```

When we are now looking at our pods

```jsx
kubectl get pods
```

We should not see any more pods listed.