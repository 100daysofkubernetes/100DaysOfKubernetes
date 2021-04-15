# Running Pods 

# 100Days Resources
* [Video by Anais Urlichs](https://youtu.be/S39JmiFsheM)
* Add your blog posts, videos etc. related to the topic here!

# Learning Resources

TODO

# Example Notes

## Practical Example

Fork the following repository: [https://github.com/vfarcic/k8s-specs](https://github.com/vfarcic/k8s-specs)

```
git clone https://github.com/vfarcic/k8s-specs.git

cd k8s-specs
```

Create a mongo DB database 

```
kubectl run db --image mongo \
--generator "run-pod/v1"
```

If you want to confirm that the pod was created do: kubectl get pods

Note that if you do not see any output right away that is ok; the mongo image is really big so it might take a while to get the pod up and running.

Confirm that the image is running in the cluster

```
docker container ls -f ancestor=mongo
```

To delete the pod run

```
kubectl delete pod db
```

Delete the pod above since it was not the best way to run the pod.

- Pods should be created in a declarative format. However, in this case, we created it in an imperative way — BAD!

To look at the pod definition:

```
cat pod/db.yml
```

```
apiVersion: v1 // means the version 1 of the Kubernetes pod API; API version and kind has to be provided -- it is mandatory
kind: Pod
metadata: // the metadata provides information on the pod, it does not specifiy how the pod behaves
name: db
labels:
type: db
vendor: MongoLabs // I assume, who has created the image
spec:
containers:
- name: db
image: mongo:3.3 // image name and tag
command: ["mongod"]
args: ["--rest", "--httpinterface"] // arguments, defined in an array
```

In the case of controllers, the information provided in the metadata has a practical purpose. However, in this case, it merely provides descriptive information.

All arguments that can be used in pods are defined in [https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.17/#pod-v1-core](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.17/#pod-v1-core)

With the following command, we can create a pod that is defined in the pod.yml file 

```
kubectl create -f pod/db.yml
```

to view the pods (in json format)

```
kubectl get pods -o json
```

We can see that the pod went through several stages (stages detailed in the video on pods)

In the case of microk8s, both master and worker nodes run on the same machine.

To verify that the database is running, we can go ahead an run

```
kubectl exec -it db sh // this will start a terminal inside the running container
echo 'db.stats()'
exit
```

Once we do not need a pod anymore, we should delete it

```
kubectl delete -f pod/db.yml
```

- Kubernetes will first try to stop a pod gracefully; it will have 30s to shut down.
- After the "grace period" a kill signal is sent

Additional notes

- Pods cannot be split across nodes
- Storage within a pod (volumes) can be accessed by all the containers within a pod

### Run multiple containers with in a pod

Most pods should be made of a single container; multiple containers within one pod is not common nor necessarily desirable 

Look at 

```
cat pod/go-demo-2.yml
```

of the closed repository (the one cloned at the beginning of these notes)

The yml defines the use of two containers within one pod

```
kubectl create -f pod/go-demo-2.yml

kubectl get -f pod/go-demo-2.yml
```

To only retrieve the names of the containers running in the pod

```
kubectl get -f pod/go-demo-2.yml \
-o jsonpath="{.spec.containers[*].name}"
```

Specify the name of the container for which we want to have the logs

```
kubectl logs go-demo-2 -c db
```

- livenessProbes are used to check whether a container should be running

Have a look at 

```
cat pod/go-demo-2-health.yml
```

within the cloned repository.

Create the pod

```
kubectl create \
-f pod/go-demo-2-health.yml
```

wait a minute and look at the output

```
kubectl describe \
-f pod/go-demo-2-health.yml
```