# Terraform

# 100Days Resources
* [Video by Anais Urlichs](https://youtu.be/pbrsrKFhohk)
* Add your blog posts, videos etc. related to the topic here!

# Learning Resources
- [https://youtu.be/UleogrJkZn0](https://youtu.be/UleogrJkZn0)
- [https://youtu.be/HmxkYNv1ksg](https://youtu.be/HmxkYNv1ksg)
- [https://youtu.be/l5k1ai_GBDE](https://youtu.be/l5k1ai_GBDE)
- [https://learn.hashicorp.com/tutorials/terraform/kubernetes-provider?in=terraform/use-case](https://learn.hashicorp.com/tutorials/terraform/kubernetes-provider?in=terraform/use-case)
- [https://registry.terraform.io/providers/hashicorp/kubernetes/latest/docs/guides/getting-started](https://registry.terraform.io/providers/hashicorp/kubernetes/latest/docs/guides/getting-started)

# Example Notes


With Terraform, you have a DevOps first approach. It is an open-source tools, original developed by [HashiCorp](https://www.hashicorp.com/)

It is an infrastructure provisioning tool that allows you to store your infrastructure set-up as code.

Normally, you would have:

1. Provisioning the Infrastructure
    1. Usually has top be done within a specific order
2. Deploying the application

It has a strong open community and is pluggable by design — meaning that it is used by vast number of organisations

Allows you to manage your infrastructure as code in a declarative format. 

What would be the alternative?

- Pressing buttons in a UI — the problem with this is that it is not repeatable across machines

Especially important in the microservices world! It would otherwise be more time-consuming and error-prone to set-up all the resources needed for every microservice.

What is a declarative approach?

Current state vs. desired state

Define what the end-result should be.

When you are first defining your resources, this is less needed. However, once you are adding or changing resources, this is a lot more important. It does not require you to know how many resources and what resources are currently available but it will just compare what is the current state and what has to happen to create the desired state.

**Different stages of using Terraform:**

1. Terraform file that defines our resources e.g. VM, Kubernetes cluster, VPC
2. Plan Phase — Terraform command: Compares the desired state to what currently exists on our cluster
if everything looks good:
3. Apply Phase: Terraform is using your API token to spin up your resources

When you defined your Terraform resources, you define a provider: 

Connects you to a cloud provider/infrastructure provider. A provider can also be used to spin up platforms and manage SAAS offerings

So, beyond IAAS, you can also manage other platforms and resources through Terraform.

You can configure your provider in either of the following ways:

1. Use cloud-specific auth plugins (for example, `eks get-token`, `az get-token`, `gcloud config`)
2. Use [oauth2 token](https://registry.terraform.io/providers/hashicorp/kubernetes/latest/docs/guides/getting-started#provider-setup)
3. Use [TLS certificate credentials](https://registry.terraform.io/providers/hashicorp/kubernetes/latest/docs#statically-defined-credentials)
4. Use `kubeconfig` file by setting **both** `[config_path](https://registry.terraform.io/providers/hashicorp/kubernetes/latest/docs#config_path)` and `[config_context](https://registry.terraform.io/providers/hashicorp/kubernetes/latest/docs#config_context)`
5. Use [username and password (HTTP Basic Authorization)](https://registry.terraform.io/providers/hashicorp/kubernetes/latest/docs#statically-defined-credentials)

## Let's try out Terraform

1. [Install Terraform](https://learn.hashicorp.com/tutorials/terraform/install-cli?in=terraform/aws-get-started)
2. Initialise cloud provider
3. Run "terraform init"
4. Set-up a security group
5. Make sure that you set up the file
6. Try whether or not it works with "terraform plan" . Here we would expect something to add but not necessarily something to change or to destroy.
7. Don't display your credentials openly, you would probably want to use something like Ansible vault or similar to store your credentials, do not store in plain text
8. Once we have finished setting up our resources, we want to destroy them "terraform destroy"

tf is the file extension that Terraform uses

Note that you can find all available information on a data source.

Common question: What is the difference between Ansible and Terraform

1. They are both used for provisioning the infrastructure
2. However, Terraform also has the power to provision the infrastructure
3. Ansible is better for configuring the infrastructure and deploying the application
4. Both could be used in combination

I am doing this example on my local kind cluster — have a look at one of the previous videos to see how to set-up kind

We are using a mixture of the following tutorials

- [https://registry.terraform.io/providers/hashicorp/kubernetes/latest/docs/guides/getting-started](https://registry.terraform.io/providers/hashicorp/kubernetes/latest/docs/guides/getting-started)
- [https://learn.hashicorp.com/tutorials/terraform/kubernetes-provider?in=terraform/use-case](https://learn.hashicorp.com/tutorials/terraform/kubernetes-provider?in=terraform/use-case)

Create your cluster e.g. with kind

kind-config.yaml

```jsx
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  extraPortMappings:
  - containerPort: 30201
    hostPort: 30201
    listenAddress: "0.0.0.0"
```

Then run

```jsx
kind create cluster --name terraform-learn --config kind-config.yaml
```

For our terraform.tfvar file, we need the following information 

- `[host](https://learn.hashicorp.com/tutorials/terraform/kubernetes-provider?in=terraform/use-case#host)` corresponds with `clusters.cluster.server`.
- `[client_certificate](https://learn.hashicorp.com/tutorials/terraform/kubernetes-provider?in=terraform/use-case#client_certificate)` corresponds with `users.user.client-certificate`.
- `[client_key](https://learn.hashicorp.com/tutorials/terraform/kubernetes-provider?in=terraform/use-case#client_key)` corresponds with `users.user.client-key`.
- `[cluster_ca_certificate](https://learn.hashicorp.com/tutorials/terraform/kubernetes-provider?in=terraform/use-case#cluster_ca_certificate)` corresponds with `clusters.cluster.certificate-authority-data`.

To get those, run:
"kubectl config view --minify --flatten --context=kind-terraform-learn"

```jsx
# terraform.tfvars

host                   = "https://127.0.0.1:32768"
client_certificate     = "LS0tLS1CRUdJTiB..."
client_key             = "LS0tLS1CRUdJTiB..."
cluster_ca_certificate = "LS0tLS1CRUdJTiB..."
```

This is our terraform .tf file

```jsx
terraform {
  required_providers {
    kubernetes = {
      source = "hashicorp/kubernetes"
    }
  }
}

variable "host" {
  type = string
}

variable "client_certificate" {
  type = string
}

variable "client_key" {
  type = string
}

variable "cluster_ca_certificate" {
  type = string
}

provider "kubernetes" {
  host = var.host

  client_certificate     = base64decode(var.client_certificate)
  client_key             = base64decode(var.client_key)
  cluster_ca_certificate = base64decode(var.cluster_ca_certificate)
}

resource "kubernetes_namespace" "test" {
  metadata {
    name = "nginx"
  }
}
resource "kubernetes_deployment" "test" {
  metadata {
    name      = "nginx"
    namespace = kubernetes_namespace.test.metadata.0.name
  }
  spec {
    replicas = 2
    selector {
      match_labels = {
        app = "MyTestApp"
      }
    }
    template {
      metadata {
        labels = {
          app = "MyTestApp"
        }
      }
      spec {
        container {
          image = "nginx"
          name  = "nginx-container"
          port {
            container_port = 80
          }
        }
      }
    }
  }
}
resource "kubernetes_service" "test" {
  metadata {
    name      = "nginx"
    namespace = kubernetes_namespace.test.metadata.0.name
  }
  spec {
    selector = {
      app = kubernetes_deployment.test.spec.0.template.0.metadata.0.labels.app
    }
    type = "NodePort"
    port {
      node_port   = 30201
      port        = 80
      target_port = 80
    }
  }
}
```

Now we can run

```jsx
terraform init

terraform plan

terraform apply
```

Make sure that all your resources are within the right path and it should work.

