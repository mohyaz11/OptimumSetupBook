# Everything you wanted to know about deployment targets, roles and machine policies (but were afraid to ask)

Deployment targets are what their name implies, they are targets you will deploy the code to.  A deployment target is typically a Windows VM running a small windows service known as a tentacle.  The Octopus Deploy server connects to that tentacle and instructs it to do work.  But a deployment target is not just a tentacle.  As time has gone on we have added more and more deployment targets types.  Now there are Windows Targets, SSH Connections (for Linux machines), Azure Targets, Kubernetes Clusters, Offline Drops and Cloud Regions.  That list keeps growing and growing.  We are willing to bet that by the time you read this book that list will have changed.

When registering a deployment target there is the standard "what environment should this target be placed in" option, but you are also given a few other options, such as roles, machine policies, and tenants that tend to create some confusion and the opportunity for some misconfigurations.  In this chapter we will walk through what each of those mean as well as some best practices for configuration.  

## Naming

## Roles

## Machine Policies

## Tenants

## Leveraging Infrastructure as Code