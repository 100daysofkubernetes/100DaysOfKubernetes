> The art of moving fast but with control.

**We want to...**

* Make the deployment process observable — real time feedback 
* Write as little YAML as possible
* Manage the entire process through Git (ideally)
* Automate the entire deployment process
* Be able to roll back deployments as fast as possible
* Deploy as often as possible

**Here is my ideal case scenario**

Once the PR is created, it automatically checks whether or not the the code can be merged into the main branch. Now the main branch of a development repository should ideally relate to a development environment or similar. Once everything looks fine, it should then be possible to promote the development environment to the production environment. The things is that in this case, it cannot live on the same git repository. (Which is where the brain fog is created — since different teams solve the process of promoting from one environment to the other differently). Again, when one environment gets promoted to the next, I want to have automated checks that tell me whether or not it can be done.

Additionally, I do not want to create any unnecessary downtime. And this is where different deployment strategies come in. 

Deployment strategy refers to the way the running instance of a system is changed or upgraded 

We will look at

- Big Bang Deployments
- Rolling Deployments
- Blue - Green deployments
- A/B Testing
- Progressive Delivery
    - Canary Deployments

## Example

For the sake of this explanation, let's assume we have several nodes running. 

## Big Bang Deployments

As the name says, everything happens at once in a big bang. This leads to the old system, or whatever was in place before the big bang deployment, being replaced with the new system. We can either replace all resources, or a subset of resources that have been running as part of our old system.

Once we have rolled out our Big Bang Deployment, none of the resources that got replaced are running anymore. If something went wrong, we will have problems reverting back to the previous state of our system before the Big Bang. On a small system that can accept downtime, this might not be an issue but within a High Availability large scale distributed system, it does not sound like a good idea to use this strategy. The main benefit of Big Bang deployments is that this strategy requires minimal set-up and configuration, it is extremely simple to rollout the new resources. However, in the worst case, it will be a painful, manual, and stressful task.

## Rolling Deployments

Let's assume you have several nodes within your system that each have to be updated. With the Rolling Deployments, the system will then be configured as such (you could also do it manual, but again, please try not to do anything manually) that each node will be updated one by one. If you run 5 nodes, 1 node will run the updated system and 4 will run the previous system. This will allow you to check whether or not the update is working (Yes, we test in production). 

What's the major downside with this approach? Well, as you might have assumed, the traffic will have to be split between the new deployment and the old deployment. For everything to work as before, the update will have to be compatible with the configuration and resources prior to the deployment. Thus, this deployment strategy takes a bit more planning than doing a Big Bang rollout.

## Blue - Green Deployments

Blue - Green Deployments is simillar to rolling updates. The system is gradually changed to the new release. You have your original system running, which is your blue deployment. You will then copy your blue deployment with the new changes that you want to introduce and deploy that to the system. This will allow you to spin down parts of your blue deployment. Once you are assured/you have tested whether or not the Green deployment works like expected, you can then rollout more versions of the Green Deployment until it takes over your Blue Deployment and the Blue Deployment has completely scaled down. During this process, you would gradually shift the traffic of your application from your Blue Deplyoment to your Green Deployment.

When you are running microservices in Kubernetes, it will become a lot easier to configure Blue - Green or other advanced deployment strategies. In comparison, updating a large scale monolithic application is a lot more difficult since you will have to take care of various dependencies. 

However, in the worst case, using Blue - Green Deployments, you will have to maintain multiple servers at the same time — one for your Blue Deployment and the other one for your green Deployment. The traffic is then shifted between both servers. This is quite resource intensive.

The benefit of Blue - Green Deployments is that you can easily roll back to your Blue deployment should the Green Deployment fail.

## Progressive Delivery

Progressive Delivery is not a specific deployment strategy but a set of deployment principles. Specifically, when your team is already using CI/CD pipelines, and DevOps best practices, using Canary Deployment and A/B testing. As part of Progressive Delivery, your team would then add feature flags to its deployments.

> The essential difference on the development side between CI/CD and progressive delivery is the use of feature flags. A continuous delivery team may do A/B testing, it may do blue/green deployments, it may implement DevOps or GitOps. But unless the development team is using feature flags, they’re not doing progressive delivery. At best, they’re doing really well at CI/CD. [Source](https://www.split.io/glossary/progressive-delivery/)

Feature flags allow you to easily deploy and rollback new versions of your deployments since the update is specifically defined across your deployment. Resulting, if you have to rollback, you are not causing a massive shut-down of your deployment but just some minor inconvenience for some users. 

Additionally, using feature flags, your teams can do more targetted A/B testing. By only rolling an update out to a subset of users, and exactly knowing what has been deployed where and to whom, your teams can have high cnfidence in their deployments.

## A/B Testing

With A/B testing, you will run different services side by side as "experiments" in the same environment. The experiments (featues) are either available to users through feature flags or through separate services. It will depend on your application and use case how traffic will be routed from the old version to the new version and specific features. 

A/B testing is a specific deployment strategy that you will want to use for experimentation and testing purposes of new features. For instance, you could make the new version of your deployment available to a certain demographic or users with specific characteristics first. 

Overall, it is a cheap way to test new features in production and to gather feedback. The drawback is that A/B testing by itself is not designed to test the feature or update across your entire system.

## Canary Deployments

Using Canary Deployments, you release a new feature to a subset of your users. This way, the traffic is slowly shifted from your old deployment to your new deployment. 

Canary Deployments provide you with a lot of control over your rollout strategy. Additionally, you could gather feedback from some users first before promoting the changes across your entire production envrionment. 

While Canary Deployment require more care in setting up the deployment and automating the process, it is considered lower risk than other deployment strategies. 

## Sources

[https://launchdarkly.com/blog/what-is-progressive-delivery-all-about](https://launchdarkly.com/blog/what-is-progressive-delivery-all-about)

[https://thenewstack.io/the-rise-of-progressive-delivery-for-systems-resilience/](https://thenewstack.io/the-rise-of-progressive-delivery-for-systems-resilience/)

[https://devopsinstitute.com/progressive-delivery/](https://devopsinstitute.com/progressive-delivery/)