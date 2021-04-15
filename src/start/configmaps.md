# ConfigMaps

# 100Days Resources
* [Video by Anais Urlichs](https://youtu.be/gQgGN12hfNY)
* Add your blog posts, videos etc. related to the topic here!

# Learning Resources
TODO

# Example Notes

ConfigMaps make it possible to keep configurations separately from our application images by injecting configurations into your container. The content/injection might be configuration **files or variables.**

It is a type of Volume. 

ConfigMaps=Mount a source to a container

It is a directory or file of configuration settings.

Environment variables can be used to configure new applications. They are great unless our application is too complex.

If the application configuration is based on a file, it is best to make the file part of our Docker image. 

Additionally, you want to use ConfigMap with caution. If you do not have any variations between configurations of your app, you do not need a ConfigMap. ConfigMaps let you easily fall into the trap of making specific configuration — which makes it harder to move the application and to automate its set-up. Resulting, if you do use ConfigMaps, you would likely have one for each environment.

So what could you store within a ConfigMap

> A ConfigMap stores configuration settings for your code. Store connection strings, public credentials, hostnames, and URLs in your ConfigMap.

[Source](https://matthewpalmer.net/kubernetes-app-developer/articles/ultimate-configmap-guide-kubernetes.html)

So make sure to not store any sensitive information within ConfigMap.

We first create a ConfigMap with

```jsx
kubectl create cm my-config \
--from-file=cm/prometheus-conf.yml
```

Taking a look into that resource

```jsx
**kubectl describe cm my-config**
```

ConfigMap is another volume that, like other volumes, need to mount

```jsx
cat cm/alpine.yml
```

The volume mount section is the same, no matter the type of volume that we want to mount.

We can create a pod and make sure it is running

```jsx
kubectl create -f cm/alpine.yml

kubectl get pods
```

And then have a look inside the pod

```jsx
kubectl exec -it alpine -- \
ls /etc/config
```

You will then see a single file that is correlated to the file that we stored in the ConfigMap

To make sure the content of both files is indeed the same, you can use the following command

```jsx
kubectl exec -it alpine -- \
cat /etc/config/prometheus-conf.yml
```

The —from-file argument in the command at the beginning can be used with files as well as directories.

In case we want to create a ConfigMap with a directory

```jsx
kubectl create cm my-config \
--from-file=cm
```

and have a look inside

```jsx
kubectl describe cm my-config
```

The create a pod that mounts to the ConfigMap

```jsx
kubectl create -f cm/alpine.yml

kubectl exec -it alpine -- \
ls /etc/config
```

Make sure to delete all the files within your cluster afterwards

```jsx
kubectl delete -f cm/alpine.yml

kubectl delete cm my-config
```

Furthermore, like every other Kubernetes resource, you can define ConfigMaps through Kubernetes YAML files. This actually (probably the easiest way) — write the ConfigMap in YAML and mount it as a Volume

We can show one of our existing ConfigMaps in YAML

```jsx
kubectl get cm my-config -o yaml
```

Additionally we can take a look at this file within our repository that has both a Deployment object and a ConfigMap

```jsx
cat cm/prometheus.yml
```

```jsx
kind: ConfigMap 
apiVersion: v1 
metadata:
  name: example-configmap 
data:
  # Configuration values can be set as key-value properties
  database: mongodb
  database_uri: mongodb://localhost:27017
  
  # Or set as complete file contents (even JSON!)
  keys: | 
    image.public.key=771 
    rsa.public.key=42
```

And then create the ConfigMap like any other resource

```jsx
kubectl apply -f config-map.yaml
```

```jsx
kind: Pod 
apiVersion: v1 
metadata:
  name: pod-using-configmap 

spec:
  # Add the ConfigMap as a volume to the Pod
  volumes:
    # `name` here must match the name
    # specified in the volume mount
    - name: example-configmap-volume
      # Populate the volume with config map data
      configMap:
        # `name` here must match the name 
        # specified in the ConfigMap's YAML 
        name: example-configmap

  containers:
    - name: container-configmap
      image: nginx:1.7.9
      # Mount the volume that contains the configuration data 
      # into your container filesystem
      volumeMounts:
        # `name` here must match the name
        # from the volumes section of this pod
        - name: example-configmap-volume
           mountPath: /etc/config
```

## Source

- [https://matthewpalmer.net/kubernetes-app-developer/articles/ultimate-configmap-guide-kubernetes.html](https://matthewpalmer.net/kubernetes-app-developer/articles/ultimate-configmap-guide-kubernetes.html)
- The chapter on ConfigMaps from the book

[https://www.notion.so/researchandstudy/100DaysOfKubernetes-0c1e0de84d654e59bdf89242195abb22#61ff3e00dd2c4669aa7ae4e988c70915](https://www.notion.so/researchandstudy/100DaysOfKubernetes-0c1e0de84d654e59bdf89242195abb22#61ff3e00dd2c4669aa7ae4e988c70915)