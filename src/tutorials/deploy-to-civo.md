# Deploy an app to CIVO k3s cluster from scratch

# 100Days Resources
* [Video by Rajesh Radhakrishnan](https://www.youtube.com/watch?v=HpAlAzOjgpI)
* [Rajesh' Kitchen repo](https://github.com/rajeshradhakrishnanmvk/kitchen.git)

# Learning Resources

1. [Video by Rajesh Radhakrishnan](https://www.youtube.com/watch?v=HpAlAzOjgpI)
2. [CIVO cli essentials](https://github.com/civo/cli)
3. [CIVO marketplace](https://www.civo.com/learn/deploying-applications-through-the-civo-kubernetes-marketplace)
4. [Part 1 -  Full Stack deployment](https://www.civo.com/learn/user_guides/446)
5. [Part 2 -  MicroFrontend deployment](https://www.civo.com/learn/user_guides/549)

# CIVO Notes

> Ideas to production, CIVO helped me to make it happen...

**A rough sketch on the application**
    I came up with an application that help us create a book cover and add chapters to it. In order to build this, I thought of having an angular frontend that communicates to three .net core backend services. 

**Login and Create a K3s cluster in CIVO**
    I am using my Windows Linux System to learn K8s, also in CIVO creating a k3s cluster is really fast. Once you have the cluster, using the CIVO cli, merge the kubconfig to interact with the cluster using 'kubectl' command. Also K9s tools is also very useful to navigate through the custer. A a few handy commands I will share it to set it up. Once you download the cli, refer to the Learning resource section with useful links:

    civo apikey add <clustername> <apikey>
    civo apikey current <clustername>
    civo region current NYC1
    civo kubernetes ls
    civo kubernetes config  "PROJECT"-infra --save --merge

    kubectl config get-contexts
    kubectl config set-context "PROJECT"-infra
    kubectl config use-context "PROJECT"-infra
**Dockerize the microservices**
    
    1. Create a docker file in each microservices.
    2. Build & push the images to docker Hub.
    3. Create a CI/CD pipeline to deploy to CIVO

    docker build . -f Dockerfile -t PROJECT-web:local
    docker tag "PROJECT"-web:local <tag>/ "PROJECT"-web:v.0.2
    docker push <TAG>/ PROJECT-web:v.0.2

**Deploy using openfaas**
    
    1. Install the Openfaas from the CIVO marketplace.
    2. Download the openfaas cli and connect to CIVO repo.
    3. Push the image to the openfaas repo.

    curl -sLSf https://cli.openfaas.com | sudo sh
    export OPENFAAS_PREFIX="<tag>/"
    export DNS="<YOUR_CIVO_CLUSTER_ID>.k8s.civo.com" # As per dashboard
    export OPENFAAS_URL=http://$DNS:31112
    PASSWORD=$(kubectl get secret -n openfaas basic-auth -o jsonpath="{.data.basic-auth-password}" | base64 --decode; echo)
    echo -n $PASSWORD | faas-cli login --username admin --password-stdin

    faas-cli new --lang dockerfile api
    faas-cli build
    faas-cli push -f stack.yml # Contains all the image deployment
    faas-cli deploy -f stack.idserver.yml # individual deployment
    faas-cli deploy -f stack.web.yml

**Deploy using helm**

    1. Create a helm chart
    2. Mention the docker image for the installation
    3. Helm install to the CIVO cluster

    helm upgrade --install "PROJECT"-frontend /"PROJECT"-web/conf/charts/"PROJECT"-ui --namespace PROJECT --set app.image=<TAG>/"PROJECT"-web:latest
    helm uninstall "PROJECT"-frontend -n "PROJECT"

**Setup the SSL certificates us Let's Encrypt**

    1. Create an LetsEncrypt PROD issuer.
    2. Deploy the ingress.
    3. Troubleshoot and verify the certificated is issued.

    kubectl apply -f ./cert-manager/civoissuer.stage.yaml
    issuer.cert-manager.io/letsencrypt-stage created
    kubectl apply -f ingress-cert-civo.yaml

    kubectl get issuer -n kitchen 
    kubectl get ing -n kitchen
    kubectl get certificates -n kitchen
    kubectl get certificaterequest -n kitchen 
    kubectl describe order  -n kitchen
    kubectl describe challenges -n kitchen

**Show off**
    I used to document the process and steps I have done during the development & deployment. It will help us to review and refine it as we progress through our development

## Rancher Shared Service using Kubernauts & Popeye setup

**Get the free life time access to RaaS from https://kubernautic.com/**

    kubectl apply -f https://rancher.kubernautic.com/v3/import/<youraccesskey>.yaml
    clusterrole.rbac.authorization.k8s.io/proxy-clusterrole-kubeapiserver created
    clusterrolebinding.rbac.authorization.k8s.io/proxy-role-binding-kubernetes-master created
    namespace/cattle-system created
    serviceaccount/cattle created
    clusterrolebinding.rbac.authorization.k8s.io/cattle-admin-binding created
    secret/cattle-credentials-c9b86c5 created
    clusterrole.rbac.authorization.k8s.io/cattle-admin created
    deployment.apps/cattle-cluster-agent created

    wget https://github.com/derailed/popeye/releases/download/v0.9.0/popeye_Linux_x86_64.tar.gz
    tar -xvf  popeye_Linux_x86_64.tar.gz
    mv popeye /usr/local/bin/

    POPEYE_REPORT_DIR=/mnt/e/Kubernetes/ popeye --save --out html --output-file report.html


## Resources
* [Rancher Shared Service](https://kubernautic.com/)

* [popeye](https://popeyecli.io/)
