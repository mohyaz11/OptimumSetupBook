# Octopus Deploy Optimum Setup Book
This repo is used to store the open source book on how to setup an Octopus Deploy Server to scale up and out.  

- [Introduction](manuscript/Introduction.md)
- [Chapter 1 - Configuring Your Environments](manuscript/Environments.md)
- [Chapter 2 - Lifecycles](manuscript/Lifecycles.md)
- [Chapter 3 - Retention Policies](manuscript/RetentionPolicies.md)
- Chapter 4 - Finally...we get to a project and deployment targets (simple project and deployment targets)
- Chapter 5 - Breaking up the band, er project into pieces for fine grained deployments (breaking up the project, using project groups, and deploy a release step)
- Chapter 6 - Oh variables and library sets, you so crazy (sharing variables between projects using library sets)
- Chapter 7 - Stopping your developers from deploying to production (creating various groups and allowing specific permissions per environment)
- Chapter 8 - Trust everyone to do their jobs right, but verify them (setting up subscriptions)
- Chapter 9 - Offloading work onto workers
- Chapter 10 - Let's talk multi-tenancy
- Chapter 11 - What is Data Center, why do I need it and when should I upgrade?
- Chapter 12 - Octopus Maintenance 101 (SQL Server Maintenance, Machine Policies, etc)
- Chapter 13 - Keeping Octopus Deploy up to date

To add to this book please do the following.  These are requirements in order for everything to be picked up by LeanPub.

1) To add a new chapter please please create a file in the manuscript folder called chapter[number].md
2) All images must be added into the folder images found in the manuscript folder
3) The file name for the image should be chapter[number]-image-name.png
4) Add that chapter to book.txt in the manuscript folder.  Please follow the existing examples. 