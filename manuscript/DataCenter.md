# What is Data Center and why would I need it?

Everything we presented so far in this book can be done using the Standard Edition of Octopus Deploy.  The standard license of Octopus Deploy provides quite a bit of functionality.  You get unlimited users, projects, and workers.  You get up to three unique instances of Octopus Deploy.  You get 3 spaces per instance.  There is a lot there.  So...what exactly does Data Center provide?

## Driving Force Behind Data Center

Octopus Deploy was originally designed to run on a single server.  Our customers would scale up the resources dedicated to that server as they scaled up the number of projects, deployment targets, and users.  But eventually, a breaking point was reached when it was impossible to scale up any further.  There are only so many network connections and concurrent processes a computer can run.  

In addition, our customers had unwittingly created a massive single point of failure in their CD pipeline.  If that server were to ever go down, for whatever reason (the most common cause was Windows Patches), that would bring all deployments to a grinding halt.  When this only affected a team of 10-15 people it was an annoyance.  When it affected an entire development shop of 100s of people this became a major problem.  

Finally, a lot of our customers started treating their Octopus Servers like pets rather than chattel.  This was because of the default configuration of Octopus Deploy, where it creates all the folders on a local drive.  If that server were to ever crash then a lot of data would be lost.  And if the master key was ever lost then getting it back up and running could take quite a while.

## Data Center Overview

Octopus Deploy Data Center allows you to create a cluster of multiple Octopus Deploy Servers.  Both servers use the same master key.  They both point to the same database.  All the task logs, artifacts, and packages must be on a file share which both servers can access.

The Octopus Servers use the database to coordinate tasks.  Each server has their own task cap.  

![](images/datacenter-nodes.png)

When a task is added into the queue to be processed one of the servers will pick it up.  A task could be anything, a deployment, a health check, retention policy check, etc.  When looking at the task log you can filter by node to see which one is doing the work.

![](images/datacenter-tasks.png)

There are still some behind the scenes things only one server can do, such as processing subscriptions or scheduled tasks.  This is why you see the label `Leader` and another label `Follower`.  A Octopus Deploy Server cluster can only have one leader, but it can have multiple followers.  If the leader is offline for an extended period of time, then one of the followers will become the leader. 

## Data Center Benefits

By having multiple servers you get the ability to scale out the Octopus Deploy server horizontally rather than just vertically.  There is no longer a single point of failure.  If a restart were to occur on one of the nodes, the other servers would keep on humming along and handle the deployments.  Because the important file directories are in a shared location, this makes it easier to spin up new Octopus Deploy Servers.  When it came time to upgrade the version of Windows, you can simply stand up a new Windows server and install Octopus Deploy on it.  You would only have to point some configuration entries at the new shared file location and database.