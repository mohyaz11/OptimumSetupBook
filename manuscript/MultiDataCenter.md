# Deploying to Multiple Data Centers

In the age of cloud providers and disaster recovery, it is common to see scenarios where our users want to deploy to multiple data centers.  In some cases, a customer could be running a live/live situation, where the code is running in all the data centers.  In other cases we see customers deploying to the primary data center first and then to a disaster recovery data center.  Another typical scenario is a data center might be the "canary" where deployments occur first, after that 5% of all traffic is routed to that data center, and assuming everything looks good then the deployment is pushed to all remaining data centers.  

With some of our customers, we see all those scenarios on a single Octopus Server.  All too often we see lots of "Production" environments and a lot of unique and custom lifecycles.

![](images/datacenter-drlifecycle.png)

Scalability is a major concern with this approach.  What happens if you want to add `Production Canary 30%` as an environment?  First, you would have to add the environment.  Then you have a decision to make, do you add it to the Canary Deployments Lifecycle?  That means any project gets that new environment.  Is that good or bad (we don't know, but it is a question you would need to ask).  What about any variables scoped to environments?  How would you know which projects needed new variables added for Production Canary 30%?

> ![](images/professoroctopus.png) A good indicator something isn't quite right with your environments or lifecycles is when you have to add [Environment Name] [Qualifier], such as `Production DR` or `Production Canary 30%`.

Earlier in the book, we created three tenants, `Internal`, `Illinois Data Center`, and `Texas Data Center`.  In this chapter, we will cover how to leverage those tenants to support a multi-data center deployment.

![](images/datacenter-tenants.png)

The use case for those tenants is as follows.

- Internal: These are servers hosted at our main headquarters.  All development and testing deployments go to these servers.  
- Texas Data Center: Our primary production data center for our external applications.  That is a data center for a cloud provider, and we have VMs running.  
- Illinois Data Center: Our disaster recovery data center.  Our cloud provider has a fast connection between these two data centers.  During the day the VMs are running, whereas on the nights and weekends they are turned off.  In the event the Texas Data Center goes offline the load balancer automatically redirects all traffic to Illinois.

When we do a deployment, we are first going to deploy to the internal data center for Dev, Test, and Staging.  Assuming everything looks good in Staging on the internal data center, we then push to Staging for Texas and then Illinois.  For a production deployment we would deploy to Texas first, and the next day we would push to the Illinois data center.

## Infrastructure

First, we need to make some changes to our existing infrastructure.  We have already had several servers located on-premises.  The servers for Development, Testing, and Staging are shared for internal and external applications.  We want to configure those for both tenanted and non-tenanted deployments.

![](images/datacenter-internaldevserverinitial.png)

That configuration can be done by expanding out the tenant section and selecting `Include in both tenanted and untenanted deployments` and then selecting the internal tenant.

> ![](images/professoroctopus.png) This can get rather tedious if you have more than 20 targets to update.  It's a good idea to automate this using the API which will speed this up.

![](images/datacenter-internaldevserverwithtenant.png)

For the machines in the Texas and Illinois Data Centers, we only want to use those for tenanted deployments.  The tenant options for that machine will be `Include only in tenanted deployments` as well as the tenant for the machine.  Please note, that for these machines the display name now includes the data center, for example, `s-octofx-illinois-web-01`.  

> ![](images/professoroctopus.png) This is somewhat contradictory to the recommendation about environments.  Environments are used almost everywhere, lifecycles, variable scoping, step scoping, and so on.  Machine display names are used much less; they are primarily used for logging during a deployment.  While it is possible to scope a variable to them, that is rare.

![](images/datacenter-stagingdatacentermachine.png)

## Projects

Next up is the project configuration.  For this demo, we went ahead and cloned the existing projects and called them `OctoFx-Database Multi Data Center`, `OctoFx-Traffic Cop Multi Data Center`, and `OctoFx-Web Multi Data Center`.  In the real world, you wouldn't have two projects, one tenanted and the other untenanted.  

We need to configure the project to allow for multi-tenanted deployments.  Go to the project and then click on the settings link on the left.  Expand out the multi-tenant deployment section and select `Require a tenant for deployments`.

> ![](images/professoroctopus.png) While it is possible to have deployments with or without a tenant it isn't something we recommend.  The project should either require tenants or not allow tenants.  Having a project with or without a tenant makes it very confusing to most users, which increases the chances of someone making a mistake.

![](images/datacenter-projectsettings.png)

Let's take a quick step back and think about this specific project, which handles the database deployments.  What would be different between the data centers?  The database server would be different.  However, other than that, everything else should be the same.  To account for these differences, we are going to create a project template variable.  The nice thing about the project template variables is that they also support the ability to mark the value as sensitive.

![](images/datacenter-projecttemplate.png)

We will have to set up a couple of project template variables for the WebUI project, but that process is the same.  Aside from that, that is all we need to configure on the project side.

## Tenants

Finally, we need to link the data center tenants to the projects.  Go to the tenant's screen, select the tenant you wish to link, and then click the `Connect Projects` button.

![](images/datacenter-connectproject.png)

When you connect the project to the tenant, you can select the deployment environments for the tenant.  If you recall from earlier, the Illinois Data Center will have two environments, `Staging` and `Production`.

![](images/datacenter-connectingprojects.png)

After connecting the project, the screen will then notify you of missing variables.  We need to go to the variables section and fill out the database server variable.

![](images/datacenter-variablewarning.png)

After linking the other projects, the result for the Illinois Data Center will look like this.

![](images/datacenter-illnoiswithlinkedprojects.png)

The internal tenant will look like this.  Note that it is configured to only deploy to `Development`, `Testing`, and `Staging`.

![](images/datacenter-internaldatacenter.png)

Finally, we can wrap it all up with the Texas Data Center.  With regards to environments, it matches the Illinois Data Center.

![](images/datacenter-texasdatacenter.png)

When we look at the database project's dashboard, we can see the view has changed from the typical untenanted deployment dashboard.  Make note that you cannot deploy to the Texas or Illinois Data Centers for the `Development` or `Testing` environments.

![](images/datacenter-databasedashboard.png)

## Deploying Code

Creating a release is the same as before, you provide a version number and select the package you wish to deploy.

![](images/datacenter-createrelease.png)

When you deploy to development, you can choose the tenants.  The tenant is automatically pre-selected when there is a single tenant for that environment.

![](images/datacenter-deploytodev.png)

Let's get the WebUI, Database, and the traffic cop pushed up to the `Testing` environment.  If you look closely at the main dashboard, you will notice a gray box has now appeared with `1/1`.  The numerator is how many tenants have been deployed with that release.  The denominator is how many total tenants are available in that environment.  This tells us is 1 out of 1 tenant in both `Development`, and `Testing` have the `1.0.0.1` release.

![](images/datacenter-maindashboard.png)

Going to the traffic cop dashboard, you will see that the other data centers are ready to be deployed but instead of `Deploy` we see `Filtered Required`.  That is intentional.  We haven't selected the release to deploy.  You can do this by clicking on the `Filter by Release` drop-down list.

![](images/datacenter-trafficcopdashboard.png)

Once a release is selected, the `Deploy` buttons appear.  Notice that each tenant has the `Deploy` button for the `Staging` environment.  That allows us to deploy to `Staging` for the `Internal` data center first and then we can deploy to the other data centers.

![](images/datacenter-trafficcopdashboardfilterselected.png)

Clicking on the `Deploy` button allows us to choose the data centers for deployment.  A multi-tenant deployment can push to 1 to N number of tenants at a time.  Right now we are just going to pick the `Internal` data center.

![](images/datacenter-deploytostaging.png)

That deployment was successful.  

![](images/datacenter-postinternaltrafficcop.png)

Now we can deploy to both Illinois and the Texas Data Center.

![](images/datacenter-deploytoboth.png)

When clicking on the deploy button for both data centers, a navigation event will occur to a new screen.  The screen shows the deployment for each tenant is treated as a separate task.  That means deployments can run at the same time.  Which makes the window for the data centers to be out of sync much smaller (assuming the deployment to both were successful).

![](images/datacenter-multipletasks.png)

The option to deploy to internal is not there when deploying to `Production`.  Only deployments the Illinois and Texas Data Center are allowed.

![](images/datacenter-trafficcoppreprod.png)

## Conclusion

In this chapter, we walked through how to configure a multiple data center deployment using tenants.  The primary advantage to using tenants is you can keep the same lifecycle as before (Development -> Testing -> Staging -> Production), but still have the flexibility to choose which data center to deploy to.  It also provides the ability to know when specific variables are scoped to a data center which you must change.  Finally, each deployment to each data center is treated as a unique task, allowing you to deploy to both data centers at the same time.
