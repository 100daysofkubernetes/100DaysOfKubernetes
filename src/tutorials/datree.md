# Verify Kubernetes Deployments with Datree

# 100Days Resources
* [Verifying Kubernetes Deployments with Datree](https://youtu.be/MpdhrabnpYs)

# Learning Resources

1. Datree Website: [https://www.datree.io/](https://www.datree.io/)
2. Datree Documentation: [https://hub.datree.io](https://hub.datree.io/)

Whether you are just getting started with Kubernetes or you have been using Kubernetes for a long time, the different tools available can get overwhelming. However, several tools that can help you to improve your deployments. On Day 39 of 100DaysOfKubernetes, I am looking at a validation tool called Datree. 

In short, Datree enables you to check your Kubernetes manifests against best practices. It will then recommend improvements to your Kubernetes deployments for higher quality and better security of your deployments.

**Datree Overview**

> Datree is a CLI tool that supports Kubernetes admins in their roles, by preventing developers from making errors in Kubernetes configurations that can cause clusters to fail in production.

Datree allows you to check Kubernetes manifests against best practices. Instead of manually verifying deployments, which is time consuming and error-prone, developers and DevOps engineers can run Datree to automatically verify Kubernetes manifests against best practices and pre-configured rules.

Their CLI tool is open source. Meaning, you can check it right now and follow the example provided below. 

**Benefits of standardize tools**

Whether you are just getting started with Kubernetes and you are writing your first manifests or you have been using Kubernetes for a long time, you will want to standardize your Kubernetes deployments. 

Getting started with Kubernetes is tough and often you do not know whether the manifests you wrote are following best practices. Using Datree can help you cross-check your deployments and improve them with the help of configured policies.

Similarly, if you are working in a large team and you have to ensure communication between multiple teams, using the same frameworks and practices can facilitate better hand-offs and collaboration between teams.

**Additional benefits**

- Great UX and onboarding experience → you can get started with Datree right away
- Little to no learning curve involved → If you are already familiar with
- Enable additional checks with every pull request → this way, poorly written deployments will never make it into production
- Their starter tier is free and open source → enterprise and custom pricing available

**How does it work?**

Datree renders your manifest files and runs it against a list of preconfigured rules — more than 90 different rules are possible. These rules are based on a list of [post mortems](https://k8s.af/) collected from companies that are using Kubernetes as well as Kubernetes best practices per release schema. 

**Using ONE validation tool**

With Datree, you can both validate the Kubernetes Schema used as well as the Kubernetes manifests. Instead of using multiple tools, you can use Datree for both. 

## Tutorial

**Installation**

Install Datree from the [documentation](https://hub.datree.io/).

On Linux and MacOS, you can use the following command:

```yaml
curl https://get.datree.io | /bin/bash
```

If you are on Windows, I highly suggest you to use WSL.

**Example Deployments**

In this example, we are going to use two different types of deployments.

- Pure Kubernetes Manifests
- Helm Chart

**Pure Kubernetes Manifests**

With the following command, we can test an example Kubernetes manifest within the installed datree repository. 

```yaml
datree test ~/.datree/k8s-demo.yaml
```

This command will cross-check the Kubernetes manifest against the configured policies.

Additionally, you will be provided with a link to your Datree Dashboard. Follow the link to

- View a history of previous tests
- Configure the policies you want to use

**The Helm Integration**

If you prefer using Helm, you can use the [Datree Helm integration](https://hub.datree.io/helm-plugin) to test your Helm Charts:

```jsx
helm plugin install https://github.com/datreeio/helm-datree
```

Then, test the chart with the following command

```jsx
helm datree test <CHART_DIRECTORY>
```

If you are new to Helm, have a look at my previous videos (one and two) or use the example repository provided by Datree:

```jsx
git clone git@github.com:datreeio/examples.git

helm datree test examples/helm-chart/nginx
```

**Policy Configuration**

Each CLI invocation is running a default policy that includes 21 built-in rules. However, you can modify the policies that you want to check your deployments against.

You can review the default policies that are set through Datree on their documentation. These include:

- Containers
- CronJob
- Networking

**Automation**

It is great to use fancy tools that help you verify your workloads. However, with many tools this benefit can be irrelevant if you have to manually run tests with every deploy. Datree has a list of integrations, including:

- CircleCI
- Travis CI
- GitHub Actions

You can find a list of integrations on their [documentation](https://hub.datree.io/integrations).
