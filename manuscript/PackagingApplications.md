# Packaging Applications on the build server

In order deploy the code we will first need to package it.  Octopus Deploy supports a wide variety of package formats, including but not limited to NuGet packages, Tar packages, as well as Docker Images, Jar files and Zip files.  Choose the package type which matches your application type, use NuGet or Zip for .NET applications, JAR for Java, Docker images for Docker, etc.

The build server, Jenkins, TeamCity, TFS/Azure DevOps, Bamboo, AppVeyor, etc, is the one typically responsible for packaging applications and pushing that package to Octopus Deploy.  It is possible to package a folder without using a build server, but for the purposes of this book, we are assuming the build server is the one who is packaging the applications.

The primary reason the build server is responsible for packaging the application is because the build server monitors your source control for any changes.  Once a change is detected the build server goes into action.  

Octopus Deploy is build server agnostic.  It does not care where it gets the packages from.  All it cares about is that it gets a package to deploy.  In fact, we have written plug-ins for many of the popular build servers.  For the build servers where we don't have a plug-in we have created a command line application called `octo.exe`.  The plug-ins are wrappers for that command line application, so you will get the same functionality as the plug-ins.  

We don't think it is practical to have screenshots for every build server we integrate with.  New build servers and tools are being added all the time.  Rather than that we have a series of recommendations for build server integration.

## Build Server Process 

Our recommended build server process is:

1) Pull down changes
2) Build Code
3) Run Static Analysis
4) Run Unit Tests
5) Package application
6) Push package to Octopus Deploy
7) Create and deploy release in Octopus Deploy
8) Run integration tests

If any of the preceding steps fail then the build server will fail the build.  This ensures, at a bare minimum, that when Octopus Deploy gets any changes we know the code successfully passed analysis and unit tests.  

Also, do not start packaging and pushing the packages until all tests have completed and passed.  This way the build server doesn't waste time packaging something that fails on a test step.  

> <img src="images/professoroctopus.png" style="float: left;"> Analysis and tests get exponentially more expensive as you move farther away from the build server.  In terms of time, fixing a problem because of analysis failure is much less expensive than fixing a problem in production.  Unit tests should be self-contained, repeatable and fast.  While integration tests are more "soup to nuts" tests which require external components such as a database or file system.  They tend to be much slower and more expensive to maintain. We've seen some projects with 10,000+ unit tests and a couple of hundred integration tests.  The unit tests took a few minutes to run while the integration tests took 10+ minutes to run.  If there is a problem in the code it should fail as fast as possible.  

## One Source Control Repository or Multiple Source Control Repositories?

If you recall, the OctoFX project has two components, a database and a UI.  The question then comes up, should both components be in the same source control repository or should there be multiple source control repositories.  The answer to that depends on the build server chosen.  Several build servers have the ability to only trigger builds if files in a specific directory are changed.  For example, the source control repository has two folders

- src
- db

The src folder contains all the C# code, while the db contain all the database scripts.  You can configure your build server to have two builds, one to watch for changes to the db folder and another to watch for changes to the src folder.  

This gets a little trickier if you both the C# code and the database scripts are in the same solution.  In that case you would want to build a specific project rather than building the entire solution.

## Building and Packaging using Versioning Schemes

The Major, Minor and Patch, of the version should be stored somewhere in the source control repository or in a variable on the build server.  This provides a source of truth for the version numbers.

> <img src="images/professoroctopus.png" style="float: left;"> When compiling or building the code those values should be used to version the .dlls or .jar files or any other compiled item.  This will allow you to easily see what version is on a specific server.  

For packaging the application to ship to Octopus Deploy you should also include a build number.  For example, if you compiled `1.5.2` then you should include the build number, say 1000, in the package name `1.5.2.1000` or `1.5.2-Build1000` depending on your own internal versioning guidelines.  

> <img src="images/professoroctopus.png" style="float: left;"> Including the build number with your package allows you to have multiple builds for the same version.  Very rarely will a version have a single build which makes it all the way to production.

## Releases

We recommend having the build server create and deploy a release to a lower environment such as dev.  Having your build server handle release creation provides you with greater control over the release.  For example, you can select the channel to use or which tenant to deploy to.  In addition, every one of our build plug-ins as well as `octo.exe` have the ability to wait for a deployment to complete.  We recommend configuring your build server to also wait for the deployment to complete. 

> <img src="images/professoroctopus.png" style="float: left;">  Having the build server keep track of your deployment opens up other options.  You can fail a build if it cannot be successfully deployed to development and let the appropriate people know of an issue quickly.  You can also configure integration tests to run after the deployment is complete.

It is possible to configure the Octopus Deploy server to automatically create a release when a package is pushed, however it only works if a very specific set of conditions are met, such as only using the internal NuGet feed, not using variables for package ids, and so on.  As your Octopus Deploy instance is used by more and more people within your company you will often find those conditions very constricting.  Automatic release creation using Octopus Deploy should be treated as an exception rather than a rule.

## Conclusion

There we have it.  We now have an entire CI/CD pipeline, from check-in all the way to deployment covered.  So far we have covered a fairly simple application and deployment scenario.  And we don't even have permissions configured.  In upcoming chapters we will dive into more advanced features such as permissions, tenants, workers.