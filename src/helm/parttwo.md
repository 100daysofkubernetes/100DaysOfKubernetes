# Helm Commands

# 100Days Resources
* [Video by Anais Urlichs](https://youtu.be/puuieUIRyjw)
* Add your blog posts, videos etc. related to the topic here!

# Learning Resources
TODO

# Example Notes

Primary features of the Helm client

**How does Helm interact with your Kubernetes cluster?**

It interacts with the same configuration files that kubectl uses

What repository can you use to store your Helm Artifacts?

The [artifact hub](https://artifacthub.io/) can be used — however, Helm 3 does not have a default hub, thus, you could also use another one.

A Helm chart is an individual package that can be installed into your Kubernetes cluster. Source

Adding a chart to your helm cli (I guess that's how you would say it)

```jsx
helm repo add bitnami https://charts.bitnami.com/bitnami
```

You can find the URLs here [https://artifacthub.io/](https://artifacthub.io/). Note that whenever you add a new Chart, it will automatically take the latest release.

We can then view all of our repositories through

```jsx
helm repo list
```

If we already have multiple charts installed, we could use the following command to look for specific charts

```jsx
helm search repo drupal
```

Looking at the Helm Chart, there are two very important distinctions:

A chart version is the version of the Helm chart. The app version is the version of the application packaged in the chart.

You can find these (and set them) in the Charts file within your chart.

Furthermore, you can install the exact same chart multiple times — e.g. if you want to run multiple applications in parallel.

An installation of a chart is a specific instance of the chart. One chart may have many installations.

Note that you can use the name of a Helm chart always only once. If you use the name myname — you cannot install another Helm chart with that exact name *within the same cluster namespace

On each installation, the configurations of the Helm chart get installed again.

You can use the following command to see the values that have been used in the previous installation:

```jsx
helm get values <name of the chart>
```

The following commands first install a chart, then you can upgrade the installation

```
helm install mysite bitnami/drupal --values values.yaml 
helm upgrade mysite bitnami/drupal --values values.yaml
```

You would want to upgrade your Helm chart when you are using different values or your application has changed.

The helm template command can be used similarly to the helm —dry-run command explored in 

[Day 21: Helm](https://www.notion.so/Day-21-Helm-b3d335a14e1441f9a2dd97568d4a85d4)

```
helm template mysite bitnami/drupal --values values.yaml --set \drupalEmail=foo@example.com
```

However, this command separates the installation logic from the helm upgrade logic.The helm template command does not contact the Kubernetes server. Resulting, this command does not provide us with a complete validation of our Helm Chart. The helm template command is mainly used to access the YAML files and then use those within other tools.

If you would like to reuse the same values within your Helm Chart, you could use the —reuse flag:

```jsx
helm upgrade mysite bitnami/drupal --reuse-values
```

We call also pass new values into our chart upon installing the chart

```jsx
helm install mysite bitnami/drupal --set drupalUsername=admin
```

This is done with the —set command. However, you should avoid injecting custom values into the Helm Chart as much as possible since this will make it difficult to keep observability of your Helm resources.

Whenever you install a new Helm chart, Helm creates a secret that tracks the previous version of the installation; you can view all the secrets with

```jsx
kubectl get secret
```

Every time the Helm Chart is installed or upgraded, Helm automatically increments its version. This is done up to ten releases.

A release can be in several different stages

pending-install
Before sending the manifests to Kubernetes, Helm claims the installation by creating a release (marked version 1) whose status is set to pending-install.

deployed
As soon as Kubernetes accepts the manifest from Helm, Helm updates the release record, marking it as deployed.

pending-upgrade
When a Helm upgrade is begun, a new release is created for an installation (e.g., v2), and its status is set to pending-upgrade.

superseded
When an upgrade is run, the last deployed release is updated, marked as superseded, and the newly upgraded release is changed from pending-upgrade to deployed.

pending-rollback
If a rollback is created, a new release (e.g., v3) is created, and its status is set to pending-rollback until Kubernetes accepts the release manifest. Then it is marked deployed and the last release is marked superseded.

uninstalling
When a helm uninstall is executed, the most recent release is read and then its status is changed to uninstalling.

uninstalled
If history is preserved during deletion, then when the helm uninstall is complete, the last release’s status is changed to uninstalled.

failed
Finally, if during any operation, Kubernetes rejects a manifest submitted by Helm, Helm will mark that release failed.

To access information on our installed Charts, we can use

```jsx
helm ls
```

This will provide us with a list of installed Helm Charts.

With the 

```jsx
helm get
```

command, we can access further information of some part of the information that is tracked by a helm release.

To print the release notes

```jsx
helm get notes mysite
```

Note that mysite in this case is the name of my Helm chart.

To print the values of a chart

```jsx
helm get values mysite
```

If we add the —all flag at the end, we can see all of the values that have been used within the Helm Chart.

To print the YAML manifests used for the deployment

```jsx
helm get manifest wordpress
```

List all you charts

```jsx
helm list
```

You can view the details of a specific chart by 

```jsx
helm history <chart name>
```

You can also rollback to a previous release

```jsx
helm rollback wordpress 2
```

In this case, we specify the name of the Helm Chart and the Version that we want to rollback to.

We can remove a chart with the 

```jsx
helm uninstall <chart name>
```

command. However, that will delete the history of our deployments.

If we want to keep the history of our deployments, we can use the fllowing

```jsx
helm uninstall <chart name> --keep-history
```

The great thing about reading the book itself is actually that I learn a lot about Kubernetes as well. The authors draw several comparisons between the way Kubernetes has been developed and how those properties translate to Helm.