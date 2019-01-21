# Making sure your server can connect to deployment targets using machine policies

A topic that we have been dancing around for the past several chapters is machine policies.  You probably noticed it on the left hand menu and didn't think much of it.

![](images/machinepolicies-machinepoliciesmenu.png)

The Octopus Deploy server needs to make sure that it can still connect to all the deployment targets.  We call that a health check.  It runs periodically on the Octopus Deploy server.  It does this because a lot of our users want to know about problems with a machine prior to doing a deployment.  If a system admin can fix a minor problem before it becomes a major problem, then it is well worth the effort.  It also does this as a sanity check to make sure that the server has not crashed or is about to run out of space.  By default health checks are performed every hour.  If the Octopus Server cannot connect to the machine, it will fail the health check.  All machines are added to the default machine policy when no policy is specified.  This is configurable on the machine policy screen.

![](images/machinepolicies-defaultmachinepolicy.png)

When a machine policy performs a health check, it queues a task on the Octopus Server.  That is a blocking task for any deployments.  The server needs to make sure the machines are there before it can deploy code.

![](images/machinepolicies-machinepolicy.png)

Using the default machine policy for less than a hundred machines is fine.  The health checks are quick.  It starts to cause problems when you have thousands of machines.  A health check could take hours to complete.  It can only connect to 10 machines at a time to perform the health check, it can take a while to finish up.  

You also might have a group of machines which are flaky and tend to go up and down.  Perhaps they are test instances or they are hosted on a client's site and your connection is spotty.  You expect them to fail once in a while.  You shouldn't change your default machine policy to account for one or two spotty machines.  The other machines in the machine policy should be running.  You want to know about those if they go down.

Our recommendation is to create several machine policies.  Some possible strategies are:

1) Machine policy per environment
2) Machine policy per data center
3) Machine policy per tenant (if the tenant is hosting their own servers and you deploy to them)
4) Transient Machine Policy (for machines which tend to go offline randomly)

If you go the machine policy per environment we recommend changing the times between health checks.  Something large for dev, say once a day, while production stays at once an hour. This is because dev is deployed to several times per day while production is deployed to once a day or once a week. Dev machines tend to go up and down at random, so you can change those to not fail the health check.  

![](images/machinepolicies-multiplemachinepolicies.png)

> ![](images/professoroctopus.png) Each machine policy health check is a unique task.  This will allow you to multi-thread your health checks.  If you are using Octopus Deploy's High Availability functionality (available with data center licenses) the health checks can now be spread across multiple nodes.  

You change the machine policy for a specific deployment target by going to the deployment target screen.

![](images/machinepolicies-configuremachinepolicy.png)

## Conclusion

In this chapter, we set up multiple machine policies to ensure we have a high signal to noise ratio if/when Octopus Deploy cannot connect to a tentacle.  Production is checked the most because typically that has the least number of deployments.  An environment such as development is getting deployed to hundreds of times a day and if a machine were to go down most people would know about it but it would have the smallest impact on the business.