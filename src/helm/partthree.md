# Setting up and modifying Helm Charts

# 100Days Resources

* [Video by Anais Urlichs](https://youtu.be/dvQErXPnjtI)
* Add your blog posts, videos etc. related to the topic here!

# Learning Resources

* https://helm.sh/docs/chart_template_guide/getting_started/

# Example Notes

Charts are the packages that Helm works with — these are then turned into Kubernetes manifests and installed on your cluster.

You can easily go ahead and create a chart template

```jsx
helm create <name of your choice>
```

> Chart names must be lower case letters and numbers. Words may be separated with dashes (-) \
  YAML files should be indented using two spaces (and never tabs).

This chart is based on the nginx image.

This chart template is especially useful if you are developing a stateless chart — we will cover other starting points later on.

With the following command, you can go ahead and install the chart

```jsx
helm install <chart name> <location of your chart>
```

as we have seen in the previous days.

Note that your Kubernetes cluster needs to have access to the Docker Hub to pull the image from there and use it.

This chart runs Nginx out of the box — the templates within the charts are used to set-up the Kubernetes manifests.

* Helm Template Syntax and creating Templates

> Helm uses the Go text template engine provided as part of the Go standard library. The syntax is used in kubectl (the command-line application for Kubernetes) templates, Hugo (the static site generator), and numerous other applications built in Go.

Note that you do not have to know Go to create templates.

To pass custom information from the Chart file to the template file, you have to use annotation.

* You can control the flow of the template generation using:

  * `if/else` for creating conditional blocks.

    ```jsx
    {{- if .Values.ingress.enabled -}}
    ...
    {{- else -}}
    # Ingress not enabled
    {{- end }}
    ```

    The syntax is providing an **"if/then"** logic — which makes it possible to have default values if no custom values are provided within the values.yaml file.

  * `with` to set a scope to a particular object
  
    ```jsx
    containers:
      - name: {{ .Release.name }}
        {{- with .Values.image }}
        image:
          repository: .repo
          pullPolicy: .pullPolicy
          tag: .tag
        {{- end }}
    ```

    while the values.yaml file contains

    ```yaml
    image:
      repo: my-repo
      pullPolicy: always
      tag: 1.2.3
    ```

    In this example the scope is changed from the current scope which is `.` to another object which is `.Values.image`.
    Hence, `my-repo`, `always` and `1.2.3` were referenced without specifying `.Values.images.`.

    > WARNING: Other objects can not be accessed using `.` from within a restricted scope. A solution to this scenario will be
        using variables `$`.

  * `range`, which provides a "for each"-style loop

    ```jsx
    cars: |-
      {{- range .Values.cars }}
      - {{ . }}
      {{- end }}
    ```

    with the values.yaml file contains

    ```yaml
    cars:
      - BMW
      - Ford
      - Hyundai
    ```

    Note that `range` too changes the scope. But to what?
    In each loop the scope becomes a member of the list. In this case, `.Values.cars` is a list of strings, so each iteration the
    scope becomes a string. In the first iteration `.` is set to `BMW`. The second iteration, it is set to `Ford` and in the third
    it is set to `Hyundai`. Therefore, each item is referenced using `.`

    ```jsx
    containers:
      - name: {{ .Release.name }}
        env:
          {{- range .Values.env }}
          - name: {{ .name }}
            value: {{ .value | quote }}
          {{- end  }}
    ```

    while the values.yaml file contains

    ```yaml
    env:
      - name: envVar1
        value: true
      - name: envVar2
        value: 5
      - name: envVar3
        value: helmForever
    ```

    In this case, `.Values.env` is a list of dictonary, so each iteration the scope becomes a dictionary.
    For example, in the first iteration, the `.` is the first dictionary `{name: envVar1, value: true}`.
    In the second iteration the scope is the dictionary `{name: envVar2, value: 5}` and so on.
    In addition, you can perform a pipeline on the value of `.name` or `.value` as shown.

* Special Functions

  * With the **include** function, objects from one template can be used within another template; the first argument is the name of the template, the "." that is passed in refers to the second argument in the root object.

  ```yaml
  metadata:
    name: {{ include "helm-example.fullname" . }}
  ```

  * With the **required** function, you can set a parameter as required. In case it's not passed a custom error message will be prompted.

  ```yaml
  image:
      repository: {{ required "An image repository is required" .Values.image.repository }}
  ```

* You can also add your own variables to tempaltes

```jsx
{{ $var := .Values.character }}
```

* The "toYaml" function turns data into YAML syntax

> Helm has the ability to build a chart archive. Each chart archive is a gzipped TAR file with the extension .tgz. Any tool that can create, extract, and otherwise work on gzipped TAR files will work with Helm’s chart archives. Source. Learning Helm Book

* Pipelines

In Helm, a pipeline is a chain of variables, commands and functions which is used to alter a value before placing it in the values.yaml file.

> The value of a variable or the output of a function is used as the input to the next function in a pipeline. The output of the final element of a pipeline is the output of the pipeline. The following illustrates a simple pipeline:
character: `{{ .Values.character | default "Sylvester" | quote }}`

* Writing maintainable templates, here are the suggestions by the maintainers

> You may go long periods without making structural changes to the templates in a chart and then come back to it. Being able to quickly rediscover the layout will make the processes faster.

Other people will look at the templates in charts. This may be team members who create the chart or those that consume it. Consumers can, and sometimes do, open up a chart to inspect it prior to installing it or as part of a process to fork it.

When you debug a chart, which is covered in the next section, it is easier to do so with some structure in the templates.

* You can package your Helm chart with the following command.

```jsx
helm package <chart name>
```

  It is important to think of a Helm chart as a package.
This package can be published to a public or private repository.
Helm repositories can also be hosted in GitHub, GitLab pages, object storage and using Chartmuseum.
You can also find charts hosted in many distributed repositories hosted by numerous people and organizations through _Helm Hub_ (aka [Artifact Hub](https://artifacthub.io/)).
