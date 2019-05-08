# Terms

Let's start by defining some terms and what they mean in the Octopus world. These terms might match up with terminology that you use. They might differ slightly. But it'll help to have a shared glossary as we work through the upcoming chapters.

## Target / Deployment Target / Machine

Targets, deployment targets and machine are the machines and services where you deploy your software. They might be physical machines, virtual machines, or PaaS targets in the cloud.

In early versions of Octopus, we used the name machines instead of deployment targets, but after we added targets like Azure Web Applications and Kubernetes, machine became a misnomer.  You might be deploying to a machine but you might also be deploying to an API.  Deployment target describes both usages.

## Environment

An environment is a group of deployment targets. Your Development environment might be one server that hosts a web application, a service, and a database server. Your Test environment may include a web application server, a service server, and a database server. Your Production environment might have multiple web application servers, service servers, and databases.  With Octopus Deploy, you have the freedom to model your environments to match your company's infrastructure.

## Package

A package is an archive (zip, tar, NuGet) that contains your application assets. Octopus works by deploying your packages to deployment targets. You can host packages in external repositories or the built-in Octopus repository. Octopus can integrate with external repositories like Artifactory and NuGet.

## Project

Projects define your deployment process, configuration variables, and the deployment lifecycle. Think of it as a blueprint for releasing your packages to deployment targets.

## Release

A release is a snapshot of your deployment process, configuration variables, and software packages.  Releases are created from projects.  Releases are deployed to the environments defined in the project lifecycle.

## Deployment

A deployment is the application of a release to an environment. Octopus executes the deployment process steps to transfer, configure, and install your packages.

The same release can be (and should be) deployed to multiple environments. You should deploy the same binaries to each of your environments using the same process.

## Lifecycle

A lifecycle defines which environments you can deploy a release to and in what order.
