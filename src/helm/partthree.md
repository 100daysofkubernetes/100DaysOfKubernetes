# Setting up and modifying Helm Charts

# 100Days Resources
* [Video by Anais Urlichs](https://youtu.be/dvQErXPnjtI)
* Add your blog posts, videos etc. related to the topic here!

# Learning Resources
TODO

# Example Notes

Charts are the packages that Helm works with — these are then turned into Kubernetes manifests and installed on your cluster.

You can easily go ahead and create a chart template

```jsx
helm create <name of your choice>
```

This chart is based on the nginx image.

This chart template is especially useful if you are developing a stateless chart — we will cover other starting points later on.

With the following command, you can go ahead and install the chart

```jsx
helm install <chart name> <location of your chart>
```

as we have seen in the previous days.

Note that your Kubernetes cluster needs to have access to the Docker Hub to pull the image from there and use it.

This chart runs Nginx out of the box — the templates within the charts are used to set-up the Kubernetes manifests.

- The syntax is providing an "if/then" logic — which makes it possible to have default values if no custom values are provided within the Values file.
- With the include function, objects from one template can be used within another template; the first argument is the name of the template, the "." that is passed in refers to the second argument in the root object.
- The "toYaml" function turns data into YAML syntax
- Go templates have if else statements, here is an example

```jsx
{{- if .Values.ingress.enabled -}}
...
{{- else -}}
# Ingress not enabled
{{- end }}
```

> Helm has the ability to build a chart archive. Each chart archive is a gzipped TAR file with the extension .tgz. Any tool that can create, extract, and otherwise work on gzipped TAR files will work with Helm’s chart archives. Source. Learning Helm Book

- You can also add your own variables to tempaltes

```jsx
{{ $var := .Values.character }}
```

You can package your Helm chart with the following command

```jsx
helm package <chart name>
```

Helm Template Syntax and creating Templates

> Helm uses the Go text template engine provided as part of the Go standard library. The syntax is used in kubectl (the command-line application for Kubernetes) templates, Hugo (the static site generator), and numerous other applications built in Go.

Note that you do not have to know Go to create templates.

To pass custom information from the Chart file to the template file, you have to use annotation.

Pipelines

In Helm, a pipeline in a chain of variables, commands and functions. 

> The value of a variable or the output of a function is used as the input to the next function in a pipeline. The output of the final element of a pipeline is the output of the pipeline. The following illustrates a simple pipeline:
character: {{ .Values.character | default "Sylvester" | quote }}

Writing maintainable templates, here are the suggestions by the maintainers

> You may go long periods without making structural changes to the templates in a chart and then come back to it. Being able to quickly rediscover the layout will make the processes faster.

Other people will look at the templates in charts. This may be team members who create the chart or those that consume it. Consumers can, and sometimes do, open up a chart to inspect it prior to installing it or as part of a process to fork it.

When you debug a chart, which is covered in the next section, it is easier to do so with some structure in the templates.