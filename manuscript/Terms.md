# Terms

Let's start off by defining some terms and what they mean in the Octopus world. This might match up with your own terminology or they might differ slightly, but it'll help to have some understanding of what we mean by by Release and Project and so on as we work through the upcoming chapters.

## Target / Deployment Target / Machine

These are the machines and services that you deploy your software to. They might be physical machines, VMs, or PAAS targets in the cloud.

In the early versions of Octopus, Deployment Targets were referred to as Machines. As more types of Targets were added (Cloud Region, Offline Drop, etc), the term Machine didn't fit 100%.  Instead of deploying to Virtual Machines, you are now deploying to an API.  

## Environment

An Environment is a group of deployment targets that you deploy your software to. For example, your Development environment may consist of one server that hosts a web application and a backing service and a database server. Your Test environment may consist of a web application server, a service server, and a database server. Your Production environment may consist of multiple web application servers, service servers, and databases.  With Octopus Deploy you have the freedom to model your environments to match your company.

## Project

Projects define your deployment process, configuration variables, and the environments that your software will be deployed to. Think of it as a blueprint for releasing your applications.

## Package

A Package is an archive (zip, tar, nuget) that contains your application assets that are to be deployed. These can be hosted externally through tools like Artifactory and NuGet or internally through the built-in package repository.

## Release

A Release is a snapshot of your deployment process, configuration variables, and software packages that can be deployed to your environments. Releases are created from Projects.

## Deployment

A Deployment is the application of a Release to one of your environments. The deployment process steps are executed, your packages are extracted and deployed, and your configuration variables are applied.

The same release can be (and should be) deployed to multiple Environments. This allows you to deploy the same binaries to each of your environments with the same process.

## Lifecycle

A Lifecycle defines which environments a project's release can be deployed to and in what order.