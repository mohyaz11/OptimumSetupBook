# Upgrading Octopus Deploy

You have your Octopus installation setup, and you have your deployments cranking away daily. You start to notice the bell icon at the top of the Octopus Deploy interface announcing new changes.  Security starts asking questions about patching Octopus Deploy.  Users want to see new features being published on Twitter and through the blog every month.  It is time to define an upgrade strategy.  

In this section, we will cover a few things that you will need to consider as part of your Octopus Deploy upgrade process. 

> ![](images/professoroctopus.png) This section will not cover *how to upgrade*, it would be best if you looked at our website for the latest instructions.  

## What and How are the Octopus Deploy releases structured?

We use our version numbering scheme to help you understand the type of changes we have introduced between two versions of Octopus Deploy:

### Major version change,  breaking changes and new features.

Example: Octopus 3.x to Octopus 2018.x

Upgrading may require some manual intervention, we will provide a detailed upgrade guide.  Downgrading will be very difficult.  Pay close attention to the release notes.  Setting up a test Octopus Deploy instance to try out the upgrade is recommended.

Major breaking changes examples: Octopus 1.x to Octopus 2.x we rewrote the HTTP API.  Going from Octopus 2.x to Octopus 3.x we changed the Tentacle communication protocol and moved to SQL Server.

### Minor version change, new features, the potential for minor breaking changes and database changes.

Example: Octopus 2018.1.x to Octopus 2018.2.x
Upgrading should be easy, but rolling back will require restoring your database.  The upgrade will most likely make changes to the database schema.  There is a chance changes are made to the API.  The goal is the changes will backward compatible wherever possible. Check our release notes for more details. Check for breaking changes as they are likely but less likely that in a Major version upgrade. 

Minor breaking changes example: In Octopus 3.3 we made a change to how Sensitive Properties work in the API.

### Patch version change, small bug fixes, and computational logic changes.

Example: Octopus 2018.8.1 to Octopus 2018.8.2

Patches should be safe to update, safe to roll back.  There is more risk in not upgrading than upgrading.  Database changes are possible but are rare.  When database changes are made, it is to patch a critical bug, the change will be safe for any other patches of the same release.

Changes example: Octopus 2018.2.3 to any other patch of Octopus 2018.2. X (upgrade or downgrade). We may decide to make API changes, but any changes will be backward compatible.

## Why stay up to date with Octopus Deploy? 

There are several benefits to upgrading your Octopus Deploy instance. 

- Whenever we get reports of performance problems we do our very best to isolate them and fix them as soon as possible.  Except in rare cases, the latest edition of Octopus Deploy will be faster than the version you have installed.
- Security flaws are treated as critical and we will fix them as soon as possible.  The latest version of Octopus Deploy will have all the latest security patches.
- In 2018 a new feature was released roughly every six to eight weeks.  This includes features such as Workers, Kubernetes Support, Azure Support, and Scheduled Triggers.  Upgrading will give you access to new features.
- Any software is bound to have bugs in it.  Octopus Deploy is no different.  Each new minor version will include multiple bug fixes in it.

## When should you upgrade

This really will depend on the type of organization you are working in. Octopus has 2 types of releases and is categorized as:

* Long Term Support
* Fast Lane

Not every customer is the same, we will provide the facts so you and your organization can decide.  

### Long Term Support

Long Term Support versions of Octopus are shipped every three months and remain in support for six months.  It contains a roll-up of all changes, including new features and bug fixes, released in the previous three months.  This is the version we recommend to our customers.  Once an LTS release is created we only ship security enhancements and fixes for show-stopping bugs.  The rule of thumb is upgrading to the latest LTS release should be less risky than staying on the current LTS release. 

Choose the slow lane releases with long-term support if this sounds like your scenario:

"We prefer stability over having the latest features."
"We upgrade Octopus about every three months."
"We evaluate Octopus in a test environment before upgrading our production installation."

### Fast Lane

Features are released when they are ready, generally every four to six weeks, and ship bug fixes and minor enhancements into patches every few days.  

You should choose the fast lane releases if this sounds like your scenario:

* "We need the latest and great features and fast turnaround on small incremental enhancements and bug fixes."
* "We want to engage closely with the Octopus team, so we can help them build the best automation tooling in the world!"
* "We have automated our install process.  Upgrading to the latest version takes less than 10 minutes."

### Changing from LTS to Fast Lane

It is possible to move from an LTS release to a Fast Lane release.  Fast lane releases are almost always the highest version number you can download.  To upgrade you will need to download and install the higher version number.  Please be sure to check the release notes for any breaking changes.  

To move from the Fast Lane to the Slow Lane will involve more effort.  You will need to wait for the next LTS version to ship and then upgrade to the latest LTS version.  For example, the first LTS release, 2018.10.x was created in late December 2018.  In early 2019, the Spaces feature was introduced in 2019.1.x.  Customers who upgraded from 2018.10.x LTS to 2019.1.x to get spaces couldn't downgrade back to 2018.10.x LTS.  They had to wait until the 2019.3.x LTS release.  

## Conclusion
We recommend coming up with an upgrade strategy as soon as possible.  When we were in charge of our Octopus Deploy instances at other companies this meant being on the last patch release before a minor release.  2019.2.2 is the most recent release, we would download and install 2019.1.11.  There is no guarantee 2019.1.11 was the most stable.  2019.2.1 could've fixed a major bug.  It was always a guessing game.

LTS was designed to fix that guessing game.  LTS is stable, only releases to fix show-stopping bugs and security fixes will be issued.  For the majority of the people reading this document, we recommend LTS is their upgrade strategy.  