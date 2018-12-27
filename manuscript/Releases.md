# Let's Deploy Some Code!

In previous chapters we got the environment scaffolding put together, then we created some projects for our application and finally we wrapped it up by setting up deployment targets for the projects to push code to.  After all that (whew!) it is finally time to deploy some code!

> <img src="images/professoroctopus.png" style="float: left;"> Typically the previous chapters will take anywhere from 10 to 20 minutes to set up and configure.  It does seem like a lot at first, the overall goal of the previous chapters was to provide you the foundation to get all the scaffolding configured so you can scale your Octopus Deploy.

Octopus Deploy will deploy code using what is known as a release.  When a release is created the project, variables, and packages being used are snapshotted.  You then deploy that release to the various environments and servers.  The idea behind a release is the core concept of Octopus Deploy, you build your code once, and you deploy that same code to all environments.  The same is true of the release.  The release is created once and that same release is deployed to all environments.

In order deploy the code we will first need to package it.  Octopus Deploy supports a wide variety of package formats, including but not limited to NuGet packages, Tar packages, as well as Docker Images, Jar files and Zip files.  Choose the package type which matches your application type, use NuGet or Zip for .NET applications, JAR for Java, Docker images for Docker, etc.

## Packaging Applications on the build server

The build server, Jenkins, TeamCity, TFS/Azure DevOps, Bamboo, AppVeyor, etc, is the one typically responsible for packaging applications and pushing that package to Octopus Deploy.  It is possible to package a folder without using a build server, but for the purposes of this book, we are assuming the build server is the one who is packaging the applications.

The primary reason the build server is responsible for packaging the application is because the build server monitors your source control for any changes.  Once a change is detected the build server goes into action.  

Octopus Deploy is build server agnostic.  It does not care where it gets the packages from.  All it cares about is that it gets a package to deploy.  In fact, we have written plug-ins for many of the popular build servers.  For the build servers where we don't have a plug-in we have created a command line application called `octo.exe`.  The plug-ins are wrappers for that command line application, so you will get the same functionality as the plug-ins.  

We don't think it is practical to have screenshots for every build server we integrate with.  New build servers and tools are being added all the time.  Rather than that we have a series of recommendations for build server integration.

### Build Server Process 

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

### One Source Control Repository or Multiple Source Control Repositories?

If you recall, the OctoFX project has two components, a database and a UI.  The question then comes up, should both components be in the same source control repository or should there be multiple source control repositories.  The answer to that depends on the build server chosen.  Several build servers have the ability to only trigger builds if files in a specific directory are changed.  For example, the source control repository has two folders

- src
- db

The src folder contains all the C# code, while the db contain all the database scripts.  You can configure your build server to have two builds, one to watch for changes to the db folder and another to watch for changes to the src folder.  

This gets a little trickier if you both the C# code and the database scripts are in the same solution.  In that case you would want to build a specific project rather than building the entire solution.

### Version Numbers

The technology you're working with will, in some cases, determine the type of versioning scheme that you choose. We recommend using Semantic Versioning for your applications, unless you are deploying artifacts to a Maven repository, in which case you will need to use Maven Versions.

Consider the following factors when deciding on the versioning scheme you'll use for your applications and packages:

1. Can you trace a version back to the commit/check-in the application/package was built from? For example: We stamp the SHA hash of the git commit into the metadata component of the Semantic Version for Octopus Deploy which makes it easier to find and fix bugs. We also tag the commit with the version of Octopus Deploy it produced so you can quickly determine which commit produced a particular version of Octopus Deploy.
2. Can your users easily report a version to the development team that supports #1?
3. Will your version numbers be confusing, or will they help people understand the changes that have been made to the software? For example: bumping a major version component (first part) means there are potentially breaking changes, but bumping a patch (3rd part) should be safe to upgrade, and safe to rollback if something goes wrong.
4. Does your tool chain support the versioning scheme? For example: Octopus Deploy supports Semantic Versioning, which enables enhanced features like Channels.

The Octopus Deploy ecosystem includes a wide variety of external services which care about versions, with some of them being quite opinionated in their versioning implementations, with potential inconsistencies amongst them. Rather than implementing a "lowest common denominator" approach, we've taken a "string-based" approach. This enables you to leverage the idiomatic/natural versioning schemes of your target ecosystem.

Octopus Deploy supports regular Semantic Versioning or SemVer.  A typical example would be: `1.5.2`:

- Major
- Minor
- Patch

We also support pragmatic versioning, as not every version can fit into three numbers.  This means including a fourth number, or `1.5.2.3`:

- Major
- Minor
- Patch
- Build

Octopus Deploy also supports pre-release tags, for example `1.5.2.3-rc.1`.  

Strictly speaking about SemVer 2.0, a version like `1.5.2-rc.1` is considered a "pre-release" and `1.5.2` would be considered a "full release".  In practice, these concepts carry weight when you are talking about hierarchies of application dependencies like classical NuGet packages or NPM dependencies. This kind of strict semantic versioning enables dependency management tooling to interpret what kind of changes each package version represents. For example, they can automatically protect your software, by preventing accidental upgrades to pre-release versions, or versions that might introduce breaking changes.

A typical Maven version string is split into 5 parts:

- Major
- Minor
- Patch
- Build number
- Qualifier

The Major, Minor, Patch and Build number are all integer values.  The Qualifier can hold any value, although some qualifiers do have special meaning.

### Building and Packaging using Versioning Schemes

The Major, Minor and Patch, of the version should be stored somewhere in the source control repository or in a variable on the build server.  This provides a source of truth for the version numbers.

> <img src="images/professoroctopus.png" style="float: left;"> When compiling or building the code those values should be used to version the .dlls or .jar files or any other compiled item.  This will allow you to easily see what version is on a specific server.  

For packaging the application to ship to Octopus Deploy you should also include a build number.  For example, if you compiled `1.5.2` then you should include the build number, say 1000, in the package name `1.5.2.1000` or `1.5.2-Build1000` depending on your own internal versioning guidelines.  

> <img src="images/professoroctopus.png" style="float: left;"> Including the build number with your package allows you to have multiple builds for the same version.  Very rarely will a version have a single build which makes it all the way to production.

## Releases

