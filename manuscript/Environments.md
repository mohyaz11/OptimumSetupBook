# Environments

An interesting fact about artists is they will create what is known as a "study."  This is a sample work to try out new techniques or colors.  The size of the work is much smaller, perhaps 8"x10" compared to the final piece which could be 8'x10'.  They have no intention of selling them (though some of them do if the piece is successful enough).  The reason we bring this is up is because that is how we view a proof of concept with Octopus Deploy.  Great to learn how Octopus Deploy works and get a deployment working, but a new fresh install should be done to ensure from the beginning you have configured everything properly.

If you have an existing instance of Octopus Deploy being used to deploy to production you have a few options.  You can create a new space, which creates essentially a fresh instance of Octopus Deploy, you can stand up a new instance, or you could create new environments and lifecycles and move existing projects over to the new workflow.  It really depends on your requirements.  

## Environments

Environments are the backbone of your deployment pipeline.  It is what you move your code through.  Before you configure your machines or your projects or your lifecycles you need to configure your environments first.  

It seems every place we have worked, and the vast majority of the companies we talk to, have the same four environments, Dev, Test (or QA), Staging (or Pre-Production), and Production.  Which makes sense.  Dev is for developers to experiment on, it is very much in flux, and it is expected to go up and down quite often.  Test is used by quality assurance to write their tests against or manually test.  Staging is used as a final "sanity check" prior to production.  Production...is well production, it is what users connect to.  

When designing Octopus Deploy we didn't want to force people to use pre-defined environments.  Some companies only have three environments, while others have much more.  Not everyone uses the same naming for their environments.  One person's test is another person's QA.  It is important to us to let our customers define their environments using their own naming conventions.  

That flexibility ends up being a double edged sword.  A lot of the customers we work with have machines in multiple data centers.  The temptation is to name your environment "Production [Data Center]," or "Production Omaha." This is done because these particular customers do not want to deploy to all data centers at the same time.  Or they want to know what version of code is in each data center.  This does not scale very well.  Every time you add a new data center you will need to adjust a number of different things, such as your life cycles, retention policies, channels, and so on.  

On top of that, a number of our customers provide SaaS solutions to their customers.  Each customer gets their own set of machines and other resources.  Again the temptation is there to configure a unique set of environments for each customer "Dev [Customer Name]," "Staging [Customer Name]," and "Production [Customer Name]."  This will work for the first dozen or so customers but again it doesn't scale very well. 

In this section we will walk through our recommendations for configuring your environments to better prepare you to scale up and out your Octopus Deploy instance as you add more projects.  We will also walk through a couple of common scenarios we have seen and how to work through them.

## Environments Configuration

We recommend configuring your environments to match your company’s terminology.  But keep it general.  Think how you want to phrase it during a conversation with a non-technical person.   "I’m pushing some code up to dev" or "I’m deploying my app to production" makes a lot more sense than "I’m pushing to Dev Omaha 45."  What does Omaha mean?  The data center?  Where did 45 come from?  

> ![](images/professoroctopus.png) A good indication your environments are modelled correctly is you can explain it in a few quick sentences.  If it takes you longer than a few seconds to explain your environments then that is an indication you need to make some changes.  

Keep the list of environments under a dozen or so.  Have the standard four or five environments, such as Dev, Test, Staging and Production.  Also add in SpinUp, TearDown and Maintenance.  Those additional environments will help cover you when it is time to build up servers, tear down applications or if you want to use Octopus to perform some scheduled maintenance tasks like taking a backup of logs in production.  Keeping the number of environments low helps with configuring life cycles, channels, security, and so on.  They also keep your dashboard easy to follow.  When we encounter customers with hundreds of environments the number one complaint we hear is "this isn't scaling all that well" or "our dashboard seems to scroll horizontally forever."

![](images/chapter001-environmentlist.png)

Don't worry about the order of the environments or adding in machines just yet.  That will come a bit later.  For now we just want to get our environments created.  

## Multiple Data Center

In the world of the Azure, AWS, Google Cloud and others to want to be able to deploy to multiple data centers.  In some scenarios the software needs to be deployed in specific intervals, first to a data center in Illinois and then to one in Texas.  The temptation is there to just create more environments called "Environment - [Data Center]."  But that leads to a few issues in scalability as well as maintainability.  

A common scenario we have seen is customers deploy to an on-premise data center for dev, test and staging, but the code is hosted in data centers in Illinois and Texas.  Before pushing to production, they like to run some sanity checks in a staging environment in Illinois and Texas.  If you did environment per data center that would end up being seven environments.  When in reality you only want four.  

![](images/chapter001-multitenancyenvironments.png)

Because we don't have any targets or projects setup at this particular point in time, this is rather easy to accomplish.  For now, we will just add two new tenants to Octopus Deploy.  This is accomplished by clicking on the tenant link at the top of the screen and then clicking add tenant in the top right corner.

![](images/chapter001-datacentertenants.png)

Don't worry, we will be coming back to them in a later chapter when we start setting up our first multiple data centers project.  Just know that they are there when you need them.

> ![](images/professoroctopus.png) Adding images to your tenants makes them easier to find on the tenant screen.  You can accomplish this by clicking on the tenant and selecting settings link on the left.  On that screen you can upload an image for a tenant.

## Multiple Customers

In the same vein of deploying the same project to multiple data centers, a lot of our customers want to deploy the same project to multiple clients.  In a SaaS world we have seen this accomplished one of two ways, make the code responsible for handling the multi-tenancy.  A user logs and the code determines which settings (connection string, name, etc) to load.  For that scenario, the multi-tenancy feature in Octopus Deploy is not needed.

The other way we've seen multi-tenancy accomplished is by giving each client their own set of resources, such as websites, or web applications and a database.  In a lot of cases, the client might need a way to test, so they would need their own testing or staging set of resources as well.  Again the temptation is there to configure a unique set of environments for each customer "Dev [Customer Name]," "Staging [Customer Name]," and "Production [Customer Name]."  This will work for the first dozen or so customers but again it doesn't scale very well.

Imagine if we had five clients, an internal testing customer, Coca-Cola, Ford, Nike and Starbucks.  The internal customer deploys to all the environments, dev, test, staging and prod.  Coca-Cola and Nike have resources in test, staging and production while Ford and Starbucks only have resources in staging and production.  If we did an environment per tenant we would end up with 14 environments.  And that is just for five customers!

This is where the multi-tenancy feature really shines for our customers.  It allows them to keep the number of environments low while at the same time have the ability to create a unique workflow per client.

![](images/chapter001-multitenantapplication.png)

Fow now we are just going to create those five customers we talked about in this example, Internal, Coca-Cola, Ford, Nike and Starbucks.

![](images/chapter001-alltenants.png)

In a later chapter we will walk through configuring a suite of projects to deploy to multiple customers using the multi-tenancy feature.

## Conclusion

In this chapter we walked through of how to set up the backbone of Octopus Deploy, the environments.  The major thing to remember is to keep the number of environments small and leverage tenants to handle deployments to different data centers or customers.  In upcoming chapters we will be walking through setting up lifecycles and retention policies.  Having a small number of environments will help with that.