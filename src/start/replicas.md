# Kubernetes ReplicaSet

# 100Days Resources
- [Video by Anais Urlichs](https://youtu.be/TBkZaPDX7Us)
- Add your blog posts, videos etc. related to the topic here!

# Learning Resources
- [Kubernetes ReplicaSet official documentation](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)

# Example Notes

**ReplicaSets**

It is usually not recommended to create pods manually but instead use multiple instances of the same application; these are then identical pods, called replicas. You can specify the desired number of replicas within the ReplicaSet.

A ReplicaSet ensures that a certain number of pods are running at any point in time. If there are more pods running than the number specified by the ReplicaSet, the ReplicaSet will kill the pods.

Similarly, if any pod dies and the total number of pods is fewer than the defined number of pods, the ReplicaSet will spin up more pods.

Each pod is supposed to run a single instance of an application. If you want to scale your application horizontally, you can create multiple instances of that pod.

The pod ReplicaSet is used for scaling pods in your Kubernetes cluster.

**Such a set of replicated Pods are created and managed by a controller, such as a Deployment.**

As long as the primary conditions are met: enough CPU and memory is available in the cluster, the ReplicaSet is self-healing; it provides fault tolerance and high availibility.

It's only purpose is to ensure that the specified number of replicas of a service is running.

All pods are managed through Controllers and Services. They know about the pods that they have to manage through the in-YAML defined Labels within the pods and the selectors within the Controllers/Services. Remember the metadata field from one of the previous days — in the case of ReplicaSets, these labels are used again.

## Some Practice

Clone the following repository: [https://github.com/vfarcic/k8s-specs](https://github.com/vfarcic/k8s-specs) and enter into the root folder

```jsx
cd k8s-specs
```

Looking at the following example

```jsx
cat rs/go-demo-2.yml
```

- The selector is used to specify which pods should be included in the replicaset
- ReplicaSets and Pods are decoupled
- If the pods that match the replicaset, it does not have to do anything
- Similar to how the ReplicaSet would scale pods to match the definition provided in the yaml, it will also terminate pods if there are too many
- the spec.template.spec defines the pod

Next, create the pods

```jsx
kubectl create -f rs/go-demo-2.yml
```

We can see further details of your running pods through the kubectl describe command

```jsx
kubectl describe -f rs/go-demo-2.yml
```

To list all the pods, and to compare the labels specified in the pods match the ReplicaSet

```jsx
kubectl get pods --show-labels
```

You can call the number of replicasets by running

```jsx
kubectl get replicasets
```

ReplicaSets are named using the same naming convention as used for pods.

### Difference between ReplicaSet and Replication Controller

They both serve the same purpose — the Replication Controller is being deprecated.

### Operating ReplicaSets

You can delete a ReplicaSet without deleting the pods that have been created by the replicaset

```jsx
kubectl delete -f rs/go-demo-2.yml \
--cascade=false
```

And then the ReplicaSet can be created again

```jsx
kubectl create -f rs/go-demo-2.yml \
--save-config
```

the —save-config flag ensures that our configurations are saved, which allows us to do more specific tasks later on.