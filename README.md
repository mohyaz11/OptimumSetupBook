# Configuring your Octopus Deploy Server to scale with you
This repo is used to store the open source book on how to setup an Octopus Deploy Server to scale up and out.  

- [Introduction](manuscript/Introduction.md)
- [Terms](manuscript/Terms.md)
- [Configuring Your Environments](manuscript/Environments.md)
- [Lifecycles](manuscript/Lifecycles.md)
- [Retention Policies](manuscript/RetentionPolicies.md)
- [Finally...we get to projects](manuscript/Projects.md)
- [Everything you wanted to know about deployment targets, roles, and machine policies (but were afraid to ask)](manuscript/DeploymentTargets.md)
- [Let's deploy some code!](manuscript/Releases.md)
- [Packaging applications on your build server](manuscript/packagingapplications.md)
- [Let's talk multi-tenancy](manuscript/MultiTenancyIntro.md)
- Deploying to Multiple Customers
- [Deploying to Multiple Data Centers](manuscript/MultiDataCenter.md)
- Deploying Feature Branches
- [Configuring Emergency Bug Fix Deployments](manuscript/EmergencyBugFixes.md)
- Stopping your developers from deploying to production (creating various groups and allowing specific permissions per environment)
- Trust everyone to do their jobs right, but verify them (setting up subscriptions)
- [Offloading work onto workers](manuscript/workers.md)
- Spaces...what are they good for (almost everything!)
- [What is Data Center, why do I need it and when should I upgrade?](manuscript/DataCenter.md)
- Octopus Maintenance 101 (SQL Server Maintenance, Machine Policies, etc)
- Keeping Octopus Deploy up to date

To add to this book please do the following.  These are requirements in order for everything to be picked up by LeanPub.

1) To add a new chapter please please create a file in the manuscript folder called chapter[number].md
2) All images must be added into the folder images found in the manuscript folder
3) The file name for the image should be [sectionname]-[imagename].png
4) Add that chapter to book.txt in the manuscript folder.  Please follow the existing examples. 