# Terms

Let's start by defining some terms and what they mean in the Octopus world. These terms might match up with terminology that you use. They might differ slightly. But it'll help to have a shared glossary as we work through the upcoming chapters.

## Target / Deployment Target / Machine

These are the machines and services that you deploy your software to. They might be physical machines, virtual machines, or PaaS targets in the cloud.

In early versions of Octopus, we used the name Machines instead of Deployment Targets. After we added Targets like Azure Web Applications and Kubernetes, Machine became a misnomer.  You might be deploying to a machine but you might also be deploying to an API.  Deployment Target describes both usages.

## Environment

An Environment is a group of deployment targets. Your Development environment might be one server that hosts a web application, a service, and a database server. Your Test environment may include a web application server, a service server, and a database server. Your Production environment might have multiple web application servers, service servers, and databases.  With Octopus Deploy, you have the freedom to model your environments to match your company's infrastructure.

## Package

A Package is an archive (zip, tar, NuGet) that contains your application assets. Octopus works by deploying your Packages to Deployment Targets. You can host Packages in external repositories or the built-in repository. Octopus can integrate with external repositories like Artifactory and NuGet.

## Project

Projects define your deployment process, configuration variables, and the deployment lifecycle. Think of it as a blueprint for releasing your Packages to Deployment Targets.

## Release

A Release is a snapshot of your deployment process, configuration variables, and software packages.  Releases are created from Projects.  Releases are deployed to the Environments defined in the Project Lifecycle.

## Deployment

A Deployment is the application of a Release to an Environment. Octopus executes the deployment process steps to transfer, configure, and install your packages.

The same release can be (and should be) deployed to multiple Environments. You should deploy the same binaries to each of your environments using the same process.

## Lifecycle

A Lifecycle defines which environments that you can deploy a release to and in what order.