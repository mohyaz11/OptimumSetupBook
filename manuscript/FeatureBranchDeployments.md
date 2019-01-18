# Feature Branch Deployments

One of the great things about GIT is how easy it is to create a branch.  And having the ability to branch easily is great.  It allows for experimentation.  A developer can try out an idea in a branch and if it doesn't work they can delete the branch.  No fuss.  No muss.  Or they can start work on a new feature in a feature branch and when the feature is complete everything is merged in to the main branch.  

There eventually comes a time where developers need to get feedback.  They want to check-in the code and deploy it so someone else can take a look at it.  Often times the code is in an incomplete state.  It shouldn't be merged into master or development.  That could have some serious consequences.  Incomplete code could get pushed out to test or heaven forbid, production, before it is ready.  QA doesn't want to test imcomplete code.  

A common technique is to hide the change behind a feature flag.  But that doesn't scale all that well.  Depending on the change, there could be a lot of if/then statements for the change.  And eventually someone has to go in and clean up that feature flag.  Which causes more work.

In a perfect world, that feature branch would get its own set of resources.  In our example application, OctoFX, this means a website and a database.  Those resources would be automatically created when the user pushes the feature branch up to the server.  

If you recall in our projects chapter we said a core rule when configuring your projects is `Projects should be responsible for setting up what it needs to run.`  This means creating a website and database.  And if you followed along when we configured our projects for OctoFX you know both the database and the web server project were configured with that in mind.  The database project will create the database if it does not exist.  The WebUI project will create the website if it doesn't exist.  All of that is covered.  

We just need to make a few other tweaks to Octopus Deploy to help support this scenario.

## Lifecycle

The first question you need to answer is, what environments will the feature branch be deployed to?  With our four environments on our instance, Dev, Testing, Staging and Production, we would say Dev, Testing, and Staging.  We included staging because we often see staging is a mirror of production.  Meaning the outside world has access to it.  Maybe it is used for UAT for business owners, or as a place to get feedback from external customers.  If that is the case, you should be able to deploy to that environment with your feature branch.  There might be a case where you want to get feedback from external customers before merging the feature into the master branch.

The resources being created for the feature branch, the database and the website, have a finite lifespan.  We will need to destroy them once the feature branch has been merged into master.  As a result of that, we should include a "TearDown" environment in our lifecycle.

Finally, we don't know how far we are going to push this feature branch.  We might only push it to dev to get some quick feedback.  Or we might push it all the way to staging.  As such, each phase in the lifecycle, Dev, Testing, and Staging should be optional.  

We will create a new lifecycle to support this scenario.  

![](images/featurebranches-lifecycle.png)

## Channels

Channels allow you to make use of a lifecycle different than the project's default lifecycle.  We will be need to create a new channel to make use of this new lifecycle.

![](images/featurebranches-newchannel.png)

### Versioning

Octopus Deploy adopts a modified version of SemVer.  We are not as strict as SemVer, but we implement a number of it's requirements.  This includes support for pre-release text after the version number (IE 2018.1.9 or 1.0.0.0).  We will be making use of that pre-release text.  This will be for both the package version as well as the release number.  

We will create a version rule for this channel where it only accepts packages with pre-release text.

![](images/featurebranches-versionrules.png)

We need to go back to the default channel and tell it to only accept non pre-release tags.

![](images/featurebranches-defaultchannelrule.png)

Now we have multiple channels to deploy to.

![](images/featurebranches-multiplechannels.png)

The only downside is you will need to repeat that for each of our projects we want to deploy feature branches to.

## Process Changes

If you have been following along so far with the book, you will note everything in the process is driven by variables.  From the database deployments:

![](images/featurebranches-databasedeployments.png)

To the WebUI Deployments:

![](images/featurebranches-webuideployments.png)

We recommend driving your process using variables for this very reason.  We can change around those variables and not have to worry too much about our process.  That being said, there might be a couple of new variables we will have to use.  

### Using Output Parameters

There are two approaches to this.  Approach #1 leverages tenants.  Each feature branch becomes a tenant.  At first blush, this makes the most sense.  The problem is, that doesn't scale all that well.  Feature branches have a finite lifespan, perhaps a few days or maybe a couple of weeks.  You will be adding/removing tenants.  Yes, it is possible to automate that using the API, but that is fairly brittle.  You also might have hundreds of projects on your octopus server.  You could have hundreds or even thousands of tenants for feature branches.  If you are working on a SaaS product which is already multi-tenant, that could be very confusing to see so many additional tenants.  With that many projects, there is a good chance for feature branch naming collision.  Unless you come up with a naming standard where the feature branch has to include the name of the project.  Finally, when do you clean-up that tenant and all the additional resources?  

Approach #2 will use the pre-release tag on the package.  A step will be added into each project which supports feature branch deployments.  That step will look for the pre-release tag and if it is detected it will set an output variable.  That output variable will then be used by all the other steps in the process.  Let's walk through setting that up.

