# Do we need to need to upgrade Octopus?

So, you have your Octopus installation setup, and you have your deployments cranking away daily. Security start asking questions about patching Octopus Deploy and what your update strategy is and are you going to be regularly updating?

The only answer to this question is **Yes!!**, and in this section, we will cover a few things that you will need to consider as part of your Octopus Deploy upgrade process. 

> <img src="images/professoroctopus.png" style="float: left;"> This section will not cover *how to upgrade*, it would be best if you looked at our website for the latest instructions.  

## What and How are the Octopus Deploy releases structured?

We use our version numbering scheme to help you understand the type of changes we have introduced between two versions of Octopus Deploy:

**Major version change** =  Major breaking changes and new features.

Octopus 3.x to Octopus 2018.x
Upgrading may require some manual intervention, we will provide a detailed upgrade guide, and downgrading may be difficult.
Check our release notes for more details. Check for breaking changes as they are likely. 
> *Example* Octopus 1.x to Octopus 2.x we rewrote the HTTP API, and from Octopus 2.x to Octopus 3.x we changed the Tentacle communication protocol and moved to SQL Server.

**Minor version change** = New features, the potential for minor breaking changes and database changes.

  - Octopus 2018.1.x to Octopus 2018.2.x
  - Upgrading should be easy, but rolling back will require restoring your database.
  - We will usually make changes to the database schema.
  - We will usually make changes to the API, being backwards compatible wherever possible.
  - Check our release notes for more details. Check for breaking changes as they are likely but less likely that in a Major version upgrade. 
> *Example* In Octopus 3.3 we made a change to how Sensitive Properties work in the API.

**Patch version change** = Small bug fixes and computational logic changes.

  - Octopus 2018.8.x to Octopus 2018.8.x
  - Patches should be safe to update, safe to roll back.
  - We will very rarely make database changes, only if we absolutely must to patch a critical bug. If we do, the change will be safe for any other patches of the same release.
> *Example* Octopus 2018.2.3 to any other patch of Octopus 2018.2. X (upgrade or downgrade). We may decide to make API changes, but any changes will be backwards compatible.

## Why? 

There are a whole heap of benefits to upgrading. 

* Performance benefits
* Security Enhancements
* New Feature(s)
* Bug Fixes

## When? 

This really will depend on the type of organisation you are working in. Octopus has 2 types of releases and is categorised as:

* Long Term Support
* Fast Lane

Not every customer is the same, and we want to make it  for you and your organisation to decide, 

### Long Term Support

Long Term Support versions of Octopus are shipped every 3 months and remain in support.

Choose the slow lane releases with long-term support if this sounds like your scenario:

"We prefer stability over having the latest features."
"We upgrade Octopus about every three months."
"We evaluate Octopus in a test environment before upgrading our production installation."

### Fast Lane

Features are released when they are ready, generally every 4-6 weeks, and ship bug fixes and minor enhancements into patches every few days. 

You should choose the fast lane releases if this sounds like your scenario:

* "We need the latest and great features and fast turnaround on small incremental enhancements and bug fixes."
* "We want to engage closely with the Octopus team, so we can help them build the best automation tooling in the world!"

### Can we change lanes?

**Yes** you can move between lanes! It will need some planning but changing from the Slow Lane (LTS) to the Fast Lane will mean there is a higher version running of Octopus Server and is generally just a standard upgrade. 

To move from the Fast Lane to the Slow Lane will involve a little more time as you will generally need to wait for the next LTS version to ship and then upgrade to the latest LTS version. 