# Configuring your Octopus Deploy Server to scale with you
This repo is used to store the open source book on how to setup an Octopus Deploy Server to scale up and out.  

- [Introduction](manuscript/Introduction.md)
- [Terms](manuscript/Terms.md)
- [Configuring Your Environments](manuscript/Environments.md)
- [Lifecycles](manuscript/Lifecycles.md)
- [Retention Policies](manuscript/RetentionPolicies.md)
- [Finally...we get to projects](manuscript/Projects.md)
- [Everything you wanted to know about deployment targets and roles (but were afraid to ask)](manuscript/DeploymentTargets.md)
- [Let's deploy some code!](manuscript/Releases.md)
- [Packaging applications on your build server](manuscript/packagingapplications.md)
- [Offloading work onto workers](manuscript/workers.md)
- [Let's talk multi-tenancy](manuscript/MultiTenancyIntro.md)
- [Deploying to Multiple Customers](manuscript/MultiTenancyApps.md)
- [Deploying to Multiple Data Centers](manuscript/MultiDataCenter.md)
- Deploying Feature Branches
- [Configuring Emergency Bug Fix Deployments](manuscript/EmergencyBugFixes.md)
- [Stopping your developers from deploying to production](manuscript/TeamSecurity.md)
- Trust everyone to do their jobs right, but verify them (setting up subscriptions)
- [Spaces...what are they good for (almost everything!)](manuscript/Spaces.md)
- [Spinning up Deployment Targets and deploying to them automatically using Infrastructure as Code](manuscript/IaC.md)
- [What is Data Center, why do I need it and when should I upgrade?](manuscript/DataCenter.md)
- [Octopus Performance & Maintenance 101 (SQL Server Maintenance, Machine Policies, etc)](manuuscript/Performance&Maintenance.md)
- [Making sure your server can connect to deployment targets using machine policies](manuscript/MachinePolicies.md)
- [Keeping Octopus Deploy up to date](manuscript/Upgrade.md)

To add to this book please do the following.  These are requirements in order for everything to be picked up by LeanPub.

1) To add a new chapter please please create a file in the manuscript folder called [sectionname].md
2) All images must be added into the folder images found in the manuscript folder
3) The file name for the image should be [sectionname]-[imagename].png
4) Add that chapter to book.txt in the manuscript folder.  Please follow the existing examples. 