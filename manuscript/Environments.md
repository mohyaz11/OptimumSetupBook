

# Environments

Environments are the backbone of your deployment pipeline.  It is what you move your code through.  Before you configure anything else, you need to configure your environments.

The most common setup is four environments.  Those are Dev, Test or QA, Staging or Pre-Production, and Production.  We've seen these at previous jobs and customers that we talk to.  It makes sense for this to be a common setup.  

Dev is for developers to experiment on.  It is very much in flux, and you expect it to go up and down quite often.  

Quality assurance test functionality in the Test.  

Staging is used as a final "sanity check" before deploying to Production.  

Production is ... well, production. It is what your users connect to.

We didn't design Octopus Deploy to force people to use a set of predefined environments.  Some companies only have three environments. Others have many more.  Not everyone uses the same naming for their environments.  One person's Test is another person's QA.  It is important to us that our customers can define and name their environments in the way that best supports their needs.

In this section, we will walk through our recommendations for configuring your environments to better prepare you to scale your Octopus Deploy instance up and out as you add more projects.  We will also walk through a couple of common scenarios we have seen and how to work through them.

## Environments Configuration

We recommend configuring your environments to match your company’s terminology.  Keep it general where possible.  Think of how you want to phrase it during a conversation with a non-technical person. "I’m pushing some code up to Dev," or "I’m deploying my app to Production," makes a lot more sense than "I’m pushing to Dev Omaha 45."  What does Omaha mean?  The data center?  Where did 45 come from?  
> ![](images/professoroctopus.png) A good sign that you have well-modeled environments is that they are easy to explain.  If it takes longer than a few seconds to explain your environments, then that is a sign that you need to make some changes.  

Keep the list of environments under a dozen or so.  Have the standard four or five environments, such as Dev, Test, Staging, and Production.  For dynamic infrastructure and maintenance, you can also add SpinUp, TearDown, and Maintenance.  Those environments will help when it is time to build up infrastructure, tear down applications, or perform some scheduled maintenance tasks like taking a backup of logs in production.

Keeping the number of environments low helps with configuring lifecycles, channels, and security.  They also keep your dashboard easy to follow.  

![](images/chapter001-environmentlist.png)

Don't worry about the order of the environments or adding machines yet.  That will come a bit later.  For now, we want to focus on creating our environments.

## Multiple Data Centers

In the world of the Azure, AWS, Google Cloud, and other cloud providers, it is becoming common to deploy to multiple data centers.  In some scenarios, you might need to deploy the software in specific intervals.  For example, you might deploy to a data center in Illinois before deploying to one in Texas.

The temptation is to name your environment "Production [Data Center]" or "Production Omaha." You would do this because you might not want to deploy to all data centers at the same time.  Or you want to know what version of the code is in each data center.  This does not scale very well, unfortunately.  Every time you add a new data center, you will need to adjust many different things.  You would have to add a new Environment. Then you would add that Environment to a Lifecycle.  You would need to update variable scopes.  Those are just a few of the areas that you would have to manage.

A scenario that we have seen is customers deploy to an on-premises data center for dev, test, and staging, but production is hosted in data centers in Illinois and Texas.  Before pushing to production, they run some sanity checks in a staging environment in Illinois and Texas.  If you create an environment per data center, you would have seven environments when you only need four.  

![](images/chapter001-multitenancyenvironments.png)

Because we don't have any targets or projects set up at this particular time, this is rather easy to do.  For now, we will add two new tenants to Octopus Deploy.  We do this by clicking on the tenant link at the top of the screen and then clicking add tenant in the top right corner.

![](images/chapter001-datacentertenants.png)

Don't worry!  We will be coming back to Tenants in a later chapter when we start setting up our first multiple data centers project.  Just know that they are there when you need them.

> ![](images/professoroctopus.png) Adding images to your tenants makes them easier to find.  You can do this by clicking on the tenant and selecting settings link on the left.  On that screen, you can upload an image for a tenant.

## Multiple Customers

In the same vein of deploying the same project to multiple data centers, a lot of our customers deploy the same project to multiple clients.

Each of their customers gets their own set of machines and other resources.  Again, you might be tempted to configure a unique set of environments for each customer.  You could create "Dev [Customer Name]," "Staging [Customer Name]," and "Production [Customer Name]."  This will work for the first dozen or so customers but again it doesn't scale very well.  

Imagine if we had five clients, an internal testing customer, Coca-Cola, Ford, Nike, and Starbucks.  The internal customer deploys to all the environments, dev, test, staging, and prod.  Coca-Cola and Nike have resources in test, staging, and production while Ford and Starbucks only have resources in staging and production.  If you create an environment per tenant, you would have 14 environments.  And that is only for five customers!

This is where the multi-tenancy comes in.  It allows you to keep the number of environments low while creating a unique workflow per client.

![](images/chapter001-multitenantapplication.png)

For now, we are going to create those five customers that we talked about in this example: Internal, Coca-Cola, Ford, Nike, and Starbucks.

![](images/chapter001-alltenants.png)

In a later chapter, we will walk through configuring a suite of projects to deploy to multiple customers using the multi-tenancy feature.

## Conclusion

In this chapter, we walked through how to set up the backbone of Octopus Deploy, the environments.  The major point to remember is to keep the number of environments small and leverage tenants to handle deployments to different data centers or customers.  In upcoming chapters, we will walk through setting up lifecycles and retention policies.  Having a small number of environments will make that easier.
