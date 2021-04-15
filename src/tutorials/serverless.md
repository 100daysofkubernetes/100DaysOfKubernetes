# Serverless

# 100Days Resources
* [Video by Anais Urlichs](https://youtu.be/LLqICWKbP5I)
* Add your blog posts, videos etc. related to the topic here!

# Learning Resources
TODO

# Example Notes

> There is no such thing as serverless, there is always a server

**How do we think about Serverless?**

In the cloud, functions written in JavaScript, response to event/triggers ⇒ highly narrowed view

**Why would you want Serverless?**

1. You want to try out cool things
2. You want to stop over- or under-provisioning of your infrastructure
3. You want to try out cool things

## Serverless

Serverless: Popular operating model — infrastructure requirements are provisioned just before the Serverless workload is executed — The infrastructure resources are just needed during the execution, so there is no need to keep the infrastructure during low to now usage.

The serverless platform will likely run on containers. 

1. No Server Management is necessary
2. You only pay for the execution time
3. They can scale to 0 = no costs to keep the infrastructure up
4. Serverless functions are stateless, this promotes scalability
5. Auto-scalability ⇒ usually taken care of by the cloud provider
6. Reduced operational costs

It does not mean that there are no servers involved. Instead it is the process of ensuring that the infrastructure is provisioned automatically. In this case, the developer is less involved to managing infrastructure. 

FAAS is a compute platform for your service — they are essentially processing your code. These functions are called by events, events in this case are any trigger. 

Within Serverless, cloud providers provision event-driven architecture. When you build your app, you look for those events and you add them to your code. 

**Issues with serverless**

1. Functions/processes of your code will not finish before the infrastructure downgrades
2. Latency issues i.e. because of cold starts

Moving from IAAS to PAAS to FAAS

**Two distinctions** 

1. Not using Servers
2. FaaS (Function as a Service): Is essentially small pieces of code that are run in the cloud on stateless containers

One is about hiding operations from us

**Serverless options**

1. Google: Google Cloud Functions
2. AWS: Lambda 
3. For Kubernetes: Knative

What is a cold start?

Serverless functions can be really slow the first time that they start. This can be a problem. These articles offer some comparisons

1. [https://mikhail.io/serverless/coldstarts/aws/](https://mikhail.io/serverless/coldstarts/aws/) 
2. [https://mikhail.io/serverless/coldstarts/big3/](https://mikhail.io/serverless/coldstarts/big3/) 
3. Discussion [https://youtu.be/AuMeockiuLs](https://youtu.be/AuMeockiuLs)

**AWS has something called Provisioned Concurrenc**y that allows you to keep on x functions that are always on and ready. 

When would you not want to use Serverless:

1. When you have a consistent traffic

Be careful,

1. Different serverless resources have different pricing models, meaning it could easily happen that you accidentally leave your serverless function running and it will eat up your pocket
2. If you depend on a Serverless feature, it is easy to get vendorlocked.

## Resources

1. How to think about Serverless [https://youtu.be/_1-5YFfJCqM](https://youtu.be/_1-5YFfJCqM)

I am going to be looking in the next days at

1. Knative: Kubernetes based open source building blocks for serverless
2. Faasd: [https://github.com/openfaas/faasd](https://github.com/openfaas/faasd)