# Helm

# 100Days Resources
* [Video by Anais Urlichs](https://youtu.be/hOaA_VYhKV8)
* Add your blog posts, videos etc. related to the topic here!

# Learning Resources
- [https://codefresh.io/helm-tutorial/getting-started-with-helm-3/](https://codefresh.io/helm-tutorial/getting-started-with-helm-3/)
- [https://helm.sh/docs/intro/install/](https://helm.sh/docs/intro/install/)
- [https://github.com/cdwv/awesome-helm](https://github.com/cdwv/awesome-helm)
- Tutorial with Helm example deployments with [Mr Podtato Head](https://github.com/cncf/podtato-head)

# Example Notes

In its simplest form, Helm is a package manager for Kubernetes. It is basically used to manage Kubernetes Applications through Helm templates. 

The combination of Helm templates and values is called a Helm Chart

The goal: Make Kubernetes resources more accessible.

Helm 3 is SUPER different from Helm 2 — NO MORE TILLER 
For more information, please refer to this guide.

Each release goes through multiple stages, including its installation, upgrade, removal, and rollback. With Helm, you can group multiple microservices together and treat them as one entity. You can either create your own chart or use an existing one from Helm Artifact Hub. Once your application is packaged into a Helm chart, it can easily be shared and deployed on Kubernetes. [Source](https://codefresh.io/helm-tutorial/getting-started-with-helm-3/)

**Charts make it super easy to:**

- Create Kubernetes Manifests,
- Version your Kubernetes resources
- Share your resources
- And publish your resources

"Helm is a graduated project in the CNCF and is maintained by the Helm community."

You can also reuse existing charts, there is a separate site for Helm Charts [https://artifacthub.io/](https://artifacthub.io/)

Imagine this to be similar to the Docker Hub — just for Kubernetes in Chart format (don't quote me on this please)

**What you need to use Helm:**

1. A machine
2. Kubernetes — kubectl installed
3. A Kubernetes cluster (ANY cluster should be fine for our purposes)
4. Install Helm
5. USE Helm

**Who would I recommend to use Helm**

1. If you are a completely beginner — you don't have to know Kubernetes — take complex business knowledge, pack it up so that other people can use it
2. If you are going advanced
3. Not necessarily for HUGE applications

## We are using Helm

To install Helm, head over to the 

- Installation Guide: [https://helm.sh/docs/intro/install/](https://helm.sh/docs/intro/install/)

Ensure that Helm is actually available on your machine:

```jsx
helm version
```

Now, you can get started super easy just by running

```jsx
mkdir charts

cd charts

helm create example-chart
```

This will create a basic Helm boiler template. Have a look at the presentation that walked through the different files that we have within that Helm chart.

**The Chart Structure**

Now before we actually install our Helm Chart, we want to make sure that it is all set-up properly

```jsx
helm install --dry-run --debug example-chart ./example-chart
```

This will populate our templates with Kubernetes manifests and display those within the console. The benefit is that if you are already familiar with Kubernetes manifests, you will be able to cross-check the Kubernetes resource — and, if needed, make changes to your Kubernetes resources.

Once we are happy with what Helm will create, we can run:

```jsx
helm install example-chart ./example-chart
```

Note that in this case, the name of my chart is "example-chart" you can name your chart however, you want to; just make sure that your are specifying the right path to your Helm chart.

Now we can have a look at the installed chart:

```jsx
helm ls
```

And the history of events of our installed Chart:

```jsx
helm history example-chart
```

If you are using Helm for your deployments, you want to store your Helm charts within a Helm repository. This will allow you to access your Helm charts over time. 

Once you have deployed a Helm Chart, you can also upgrade it with new values

```jsx
helm upgrade [RELEASE] [CHART] [flags]
```

To remove our example chart:

```jsx
helm delete example-chart
```

## Second Helm Exercise

### Helm Commands

Search for all helm repositories on the helm hub: [https://artifacthub.io/](https://artifacthub.io/)

```jsx
helm search hub
```

Add a repositories

```jsx
helm repo add <name> <repository link>
e.g. helm repo add bitnami https://charts.bitnami.com/bitnami
```

From [https://artifacthub.io/packages/helm/bitnami/mysql](https://artifacthub.io/packages/helm/bitnami/mysql)

List repositories all repositories that you have installed

```jsx
helm repo list
```

Search within a repository

```jsx
helm search repo <name>
```

Instead of using the commands, you can also search the chart repository online.

To upgrade the charts in your repositories

```jsx
helm repo update
```

Install a specific chart repository

```jsx
helm install stable/mysql --generate-name

```

Note that you can either ask Helm to generate a name with the —generate-name flag,

or you can provide the name that you want to give the chart by defining it after install

```jsx
helm install say-my-name stable/mysql
```

Check the entities that got deployed within a specific cluster:

List all the charts that you have deployed with the following command

```jsx
helm ls
```

To remove a chart use

```jsx
helm uninstall <name of the chart>
```

In the next section, we are going to look at ways that you can customize your chart.

