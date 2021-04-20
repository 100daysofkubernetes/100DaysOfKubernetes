# Kubernetes Volumes

# 100Days Resources
* [Video by Anais Urlichs](https://youtu.be/qnlgJkntS1k)
* Add your blog posts, videos etc. related to the topic here!

# Learning Resources

- [https://kubernetes.io/docs/tasks/configure-pod-container/configure-volume-storage/](https://kubernetes.io/docs/tasks/configure-pod-container/configure-volume-storage/)
- [https://codeburst.io/kubernetes-storage-by-example-part-1-27f44ae8fb8b](https://codeburst.io/kubernetes-storage-by-example-part-1-27f44ae8fb8b)
- [https://youtu.be/0swOh5C3OVM](https://youtu.be/0swOh5C3OVM)

# Example Notes

We cannot store data within our containers — if our pod crashes and restarts another container based on that container image, all of our state will be lost. **Kubernetes does not give you data-persistence out of the box.**

**Volumes are references to files and directories** made accessible to containers that form a pod. So, they basically keep track of the state of your application and if one pod dies the next pod will have access to the Volume and thus, the previously recorded state.

There are over 25 different Volume types within Kubernetes — some of which are specific to hosting providers e.g. AWS

The difference between volumes is the way that files and  directories are created.

Additionally, Volumes can also be used to access other Kubernetes resources such as to access the Docker socket.

The problem is that the storage has to be available across all nodes. When a pod fails and is restarted, it might be started on a different node.

Overall, Kubernetes Volumes have to be highly error-resistant — and even survive a crash of the entire cluster.

Volumes and Persistent Volumes are created like other Kubernetes resources, through YAML files.

Additionally, we can differentiate between **remote and local volumes** — each volume type has its own use case. 

Local volumes are tied to a specific node and do not survive cluster disasters. Thus, you want to use remote volumes whenever possible.

```jsx
apiVersion: v1
kind: Pod
metadata:
  name: empty-dir
spec:
  containers:
    - name: busybox-a
      command: ['tail', '-f', '/dev/null']
      image: busybox
      volumeMounts:
        - name: cache
          mountPath: /cache
    - name: busybox-b
      command: ['tail', '-f', '/dev/null']
      image: busybox
      volumeMounts:
        - name: cache
          mountPath: /cache
  volumes:
    - name: cache
      emptyDir: {}
```

Create the resource:

```
kubectl apply -f empty-dir
```

Write to the file:

```
kubectl exec empty-dir --container busybox-a -- sh -c "echo \"Hello World\" > /cache/hello.txt"
```

Read what is within the file

```
kubectl exec empty-dir --container busybox-b -- cat /cache/hello.txt
```

However, to ensure that the data will be saved beyond the creation and deletion of pods, we need Persistent volumes. Ephemeral volume types only have the lifetime of a pod — thus, they are not of much use if the pod crashes.

A persistent volume will have to take the same storage as the physical storage. 

Storage in Kubernetes is an external plug-in to our cluster. This way, you can also have multiple different storage resources. 

The storage resources is defined within the PersistentVolume YAML

A hostPath volume mounts a file or directory from the host node’s 
filesystem into your Pod. This is not something that most Pods will 
need, but it offers a powerful escape hatch for some applications.

*— Kubernetes — [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/)*

```jsx
apiVersion: v1
kind: Pod
metadata:
  name: host-path
spec:
  containers:
    - name: busybox
      command: ['tail', '-f', '/dev/null']
      image: busybox
      volumeMounts:
        - name: data
          mountPath: /data
  volumes:
  - name: data
    hostPath:
      path: /data
```

Create the volume

```jsx
kubectl apply -f empty-dir
```

Read from the volume

```jsx
kubectl exec host-path -- cat /data/hello.txt
```