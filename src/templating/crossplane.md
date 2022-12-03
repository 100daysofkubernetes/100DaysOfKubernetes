# Crossplane

# 100Days Resources
* [Video by Anais Urlichs](https://youtu.be/Dw0SMLHZvXM)
* Add your blog posts, videos etc. related to the topic here!

# Learning Resources
- The Website [https://crossplane.io/](https://crossplane.io/)
- The Docs [https://crossplane.io/docs/v1.0/](https://crossplane.io/docs/v1.0/)

# Example Notes

Some Highlights from their website:

> Crossplane is an open source Kubernetes add-on that supercharges your Kubernetes clusters enabling you to provision and manage infrastructure, services, and applications from kubectl.

Crossplane is a CNCF sandbox project which extends the Kubernetes API to manage and compose infrastructure.

Crossplane introduces multiple building blocks that enable you to provision, compose, and consume infrastructure using the Kubernetes API. These individual concepts work together to allow for powerful separation of concern between different personas in an organization, meaning that each member of a team interacts with Crossplane at an appropriate level of abstraction.

Crossplane uses Kubernetes Custom Resource Definitions to manage your infrastructure and resources. Basically, all of the resources used are Kubernetes resources themselves, making it possible for Crossplane to interact with any other Kubernetes Resources.

In short, Crossplane manages all of your resources through a Kubernetes API.

The Benefits:

1. Manage your infrastructure directly through kubectl
2. Supports infrastructure from all major cloud providers + the maintainers are consistently working on new providers
3. You can build your own infrastructure abstraction on top of Crossplane
4. Crossplane is based on CRD, so you can run it anywhere + it is extensible
5. One place of truth for your infrastructure
6. Use custom APIs to manage policies, hiding infrastructure complexity and safely customise application
7. Declarative Infrastructure Configuration —  infrastructure managed through Crossplane is accessible via kubectl, configurable with YAML, and self-healing right out of the box.

**GitOps best practices**

Crossplane can be combined with CI/CD pipelines, this allows to implement GitOps best practices. GitOps is a deployment strategy, whereby everything, the desired state of the application is defined in git.

> infrastructure configuration that can be versioned, managed, and deployed using your favorite tools and existing processes

To use crossplane you initially need any type of Kubernetes Cluster — it could be Minikube, where you then install it! Once you have access to the Crossplane Kubernetes resources 

> On their own, custom resources let you store and retrieve structured data. When you combine a custom resource with a custom controller, custom resources provide a true declarative API.

## Install Crossplane

Crossplane combines custom resource definitions and custom controllers — those are both Kubernetes resources. So you install Kubernetes resources that are used to manage Crossplane.

[https://docs.crossplane.io/v1.10/getting-started/install-configure/](https://docs.crossplane.io/v1.10/getting-started/install-configure/)

Prerequisites

1. Have kubectl installed
2. Have Helm installed
3. Have a local Kubernetes cluster, or any Kubernetes cluster
4. Have access to the account e.g. Azure or Google where you want to provision your infrastructure

First, you need to install Corssplane on a Kubernetes cluster. Note that this can be any cluster, it does not have to be the cluster that you will be using Crossplane on to provision Infrastructure. This just provides the Corssplane interface for you to be able to use Corssplane. For instance, in my case, I am going to be using Crossplane on my Docker Cluster and then povision Infrastructure on Azure.

Note that we are going the self-provisioned route:

```jsx
kubectl create namespace crossplane-system

helm repo add crossplane-stable https://charts.crossplane.io/stable
helm repo update

helm install crossplane --namespace crossplane-system crossplane-stable/crossplane
```

Check that all resources are running properly

```jsx
helm list -n crossplane-system

kubectl get all -n crossplane-system
```

Next, we can install the Crossplane CLI that will be used in combination with kubectl: 

```jsx
curl -sL https://raw.githubusercontent.com/crossplane/crossplane/release-1.0/install.sh | sh
```

Depending on the provider that you want to use, you can select Azure, GCP, AWS, or Alibaba (as of the time of writing)

On the same cluster that you installed the previous resources on:

```jsx
kubectl crossplane install provider crossplane/provider-azure:v0.14.0
```

Now wait until the Provider becomes healthy:

```jsx
kubectl get provider.pkg --watch
```

Now it will get a bit tricks. If you are using Azure, make sure that you are logged into your account.  For that, first install the Azure CLI :

```jsx
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

And then login with:

```jsx
az login
```

Once you are logged in, you can run the following commands

```jsx
**# create service principal with Owner role
az ad sp create-for-rbac --sdk-auth --role Owner > "creds.json"

# we need to get the clientId from the json file to add Azure Active Directory
# permissions.
if which jq > /dev/null 2>&1; then
  AZURE_CLIENT_ID=$(jq -r ".clientId" < "./creds.json")
else
  AZURE_CLIENT_ID=$(cat creds.json | grep clientId | cut -c 16-51)
fi

RW_ALL_APPS=1cda74f2-2616-4834-b122-5cb1b07f8a59
RW_DIR_DATA=78c8a3c8-a07e-4b9e-af1b-b5ccab50a175
AAD_GRAPH_API=00000002-0000-0000-c000-000000000000

az ad app permission add --id "${AZURE_CLIENT_ID}" --api ${AAD_GRAPH_API} --api-permissions ${RW_ALL_APPS}=Role ${RW_DIR_DATA}=Role
az ad app permission grant --id "${AZURE_CLIENT_ID}" --api ${AAD_GRAPH_API} --expires never > /dev/null
az ad app permission admin-consent --id "${AZURE_CLIENT_ID}"**
```

Note that some of them might not work if you are not at least an owner within your Azure account.

Lastly we need to set-up a Provider Secret and our Provider

```jsx
**kubectl create secret generic azure-creds -n crossplane-system --from-file=key=./creds.json**
```

Now **either** create the following file ProviderConfig.yaml

```jsx
apiVersion: azure.crossplane.io/v1beta1
kind: ProviderConfig
metadata:
  name: default
spec:
  credentials:
    source: Secret
    secretRef:
      namespace: crossplane-system
      name: azure-creds
      key: key
```

And then run 

```jsx
kubectl apply --file ./ProviderConfig.yaml
```

Note that you might have to modify the file path depending on where the file is stored at.

Alternatively, you can run the following command

```jsx
kubectl apply -f https://raw.githubusercontent.com/crossplane/crossplane/release-1.0/docs/snippets/configure/azure/providerconfig.yaml
```

## Provision Infrastructure

This step is pretty straight forward — we can set-up a Kubernetes Yaml file that allows us to create a Kubernetes Service i.e. a Cluster on Azure:

E.g. we will name is aks in our case

```jsx
apiVersion: azure.crossplane.io/v1alpha3
kind: ResourceGroup
metadata:
  name: CHANGE_ME_RESOURCE_GROUP
spec:
  location: eastus

---

apiVersion: compute.azure.crossplane.io/v1alpha3
kind: AKSCluster
metadata:
  name: anais-demo
spec:
  location: eastus
  version: "1.19.7"
  nodeVMSize: Standard_D2_v2
  resourceGroupNameRef:
    name: CHANGE_ME_RESOURCE_GROUP
  dnsNamePrefix: dt
  nodeCount: 3
```

Have a look at the Azure documentation on how the YAML has to be defined to create different services.

Once we apply the YAML

```jsx
kubectl apply --filename ./aks.yaml
```

We can go ahead and have a look at the created resources

```jsx
kubectl get resourcegroups
```

We can watch the provisioning of our cluster through

```jsx
watch kubectl get aksclusters

kubectl describe aksclusters
```

And connect to our Azure cluster as well

```jsx
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster

az aks get-credentials --resource-group anais-resource --name anais-crossplane-demo
```

Something that Terraform, Pulumi etc. does not have — this is a feature of Crossplane

1. Correct cluster manually — do what should not be done 
2. Crossplane will keep whatever state we defined before, even if we change the state of our cluster through the UI

Add a node pool to see how crossplane will downscale the pool

```jsx
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

The next step is using GitOps to deploy our infrastructure

### Install and application

I have already set-up the example Azure voting app. Now I will deploy those resources

```jsx
kubectl apply -f ./app/voting.yaml
```

And we can get the service resource with

```jsx
kubectl get service azure-vote-front --watch
```

Since this is a service of type LoadBalancer, we can access it through the External-IP

## Uninstall Provider

Let’s check whether there are any managed resources before deleting the
provider.

`kubectl get managed`

If there are any, please delete them first, so you don’t lose the track of them.
Then delete all the `ProviderConfig`s you created. An example command if you used
AWS Provider:

`kubectl delete providerconfig.aws --all`

List installed providers:

`kubectl get provider.pkg`

Delete the one you want to delete:

`kubectl delete provider.pkg <provider-name>`

## Uninstall Crossplane

`helm delete crossplane --namespace crossplane-system

kubectl delete namespace crossplane-system`

Helm does not delete CRD objects. You can delete the ones Crossplane created with
the following commands:

`kubectl patch lock lock -p '{"metadata":{"finalizers": []}}' --type=merge
kubectl get crd -o name | grep crossplane.io | xargs kubectl delete`

Connecting Crossplane with ArgoCD

[https://aws.amazon.com/blogs/opensource/connecting-aws-managed-services-to-your-argo-cd-pipeline-with-open-source-crossplane/](https://aws.amazon.com/blogs/opensource/connecting-aws-managed-services-to-your-argo-cd-pipeline-with-open-source-crossplane/)
