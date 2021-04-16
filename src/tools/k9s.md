# K9s

# 100Days Resources
* [Video by Anais Urlichs](https://youtu.be/5Nhbl6LwP2o)
* Add your blog posts, videos etc. related to the topic here!

# Learning Resources

TODO

# Example Notes

Managing your kubernetes cluster through kubectl commands is difficult. 

- You have to understand how the different components interact, specifically, what resources is linked to which other resource
- Using a UI usually abstracts all the underlying resources, not allowing for the right learning experience to take place + forcing your to click buttons
- You do not have full insights on the resources within your cluster.

These are some of the aspects that K9s can help with.

This is a short intro and tutorial with examples on how you can use K9s to navigate your cluster

## What is k9s?

K9s is a terminal-based tool that visualises the resources within your cluster and the connection between those. It helps you to access, observe, and manage your resources. "K9s continually watches Kubernetes for changes and offers subsequent commands to interact with your observed resources." — Taken from their GitHub repository

**Installation options:**

- Homebrew MacOS and Linux
- OpenSUSE
- Arch Linux
- Chocolatey for Windows
- Install via Go
- Install from source
- Run from Docker container

As you can see there are multiple different options and I am sure you will find the right one for you and your OS.

**Some additional features:**

1. Customise the color settings
2. [Key Bindings](https://github.com/derailed/k9s#key-bindings)
3. Node Shell
4. Command Aliases
5. HotKeySpport
6. Resource Custom Columns
7. Plugins
8. Benchmark your Application

Have a look at their website for more comprehensive information

## Getting up and running

The configuration for K9s is kept in your home directory under .k9s $HOME/.k9s/config.yml.

You can find a detailed explanation on what the file is here: [https://k9scli.io/topics/config/](https://k9scli.io/topics/config/)

Note that the definitions may change over time with new releases.

To enter the K9s, just type k9s into your terminal.

Show everything that you can do with K9s, just type

```jsx
?
```

Or press:

```jsx
ctrl-a
```

Which will show a more comprehensive list.

Search for specific resources type

```jsx
:<name>
```

The name could for instance refer to "pods", "nodes", "rs" (for ReplicaSet) and other Kubernetes resources that you have already been using. Once you have selected a resource, for instance, a namespace, you can search for specific namespaces using "/"

Have a look at deployments

```jsx
:dp
```

To switch between your Kubernetes context type:

```jsx
:ctx
```

You can also add the context name after the command if you want to view your Kubernetes context and then switch.

To delete a resource type press: ctrl-d

To kill a resource, use the same command but with k: ctrl-k

Change how resourc4es are displayed:

```jsx
:xray RESOURCE
```

To exist K9s either type 

```jsx
:q
```

Or press: ctrl-c

## k9s interaction with Kubernetes

If you are changing the context or the namespace with kubectl, k9s will automatically know about it and show you the resources within the namespace.

Alternatively, you can also specify the namespace through K9s like detailed above.

## k9s for further debugging and benchmarking

K9s integrates with Hey, which is a CLI tool used to benchmark HTTP endpoints. It currently supports benchmarking port-forwards and services ([Source](https://k9scli.io/topics/bench/))

To port forward, you will need to selects a pod and container that exposes a specific port within the PodView. 

With SHIFT-F a dialog will pop up and allows you to select the port to forward to.

Once you have selected that, you can use 

```jsx
:pf
```

to navigate to the PortForward view and list out all active port-forward.

Selecting port-forward + using CTRL-B will run a benchmark on that http endpoint.

You can then view the results of the benchmark through

```jsx
:be
```

Keep in mind that once you exit the K9s session, the port-forward will be removed, forwards only last for the duration of the session.

Each cluster has its own bench-config that can be found at $HOME/.k9s/bench-<my_context>.yml

You can find further information [here](https://k9scli.io/topics/bench/).

You can debug processes using

```jsx
k9s -l debug
```

## Change Look

You can change the look of your K9s by changing the according YAML in your .k9s folder.

Here is where the default skin lives: skin.yml

You can find example skin files in the skins directory: [https://github.com/derailed/k9s/tree/master/skins](https://github.com/derailed/k9s/tree/master/skins)

View all the color definitions here: [https://k9scli.io/topics/skins/](https://k9scli.io/topics/skins/)

For further information on how to [optimise K9s](https://k9scli.io/topics/video/), check-out their video tutorials.

Extensive written tutorial: [https://medium.com/flant-com/k9s-terminal-ui-for-kubernetes-aeead8b0303f](https://medium.com/flant-com/k9s-terminal-ui-for-kubernetes-aeead8b0303f)