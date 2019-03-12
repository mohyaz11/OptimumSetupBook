# Performance & Maintenance 101

Octopus are generally hygienic creatures; they clean up after themselves.  Octopus Deploy is not different.  It does its best to clean up after itself.  However, it still needs your help.  In this chapter we will walk through some common tweaks you can make to Octopus Deploy to keep it running lean and mean.

## Routine Maintenance

Octopus Deploy works best when regular maintenance is performed.  Routine maintenance can help clear up the "cruft" left behind by old deployments.  

### Retention Policies

We cannot stress this enough.  Please set your retention policies.  Retention policies will clean old releases from your server and on the tentacles.  The one exception to this is the Events table which records an audit trail of every significant event in your Octopus.  We have an entire chapter in this book devoted to retention policies.  Please read that.

### Upgrade

It's critical to keep Octopus up to date. We are continually working to ensure that Octopus performs to a high standard.  We will always recommend upgrading to the latest LTS version of Octopus Deploy.  We have written an entire chapter in this book about upgrading Octopus Deploy.  Please refer to that for more details.

### SQL Server Maintenance

Starting with the first LTS release, 2018.10.x, any upgrade to Octopus Deploy will automatically rebuild fragmented indexes and regenerate stats.  That helps.  However, it is vital to rebuild fragmented indexes on a regular interval.  Work with a DBA to set up a SQL job to rebuild indexes and regenerate stats every week.  

Don't forget to back up the Octopus Deploy database.  A good strategy is to do a full backup once a week, a differential backup once a day and a transaction log backup once every 10-15 minutes.  If the SQL Server hosting the Octopus Deploy database were ever to crash you would only lose a few minutes worth of work.

## Scaling Octopus

The Octopus Deploy server does quite a lot of work during deployments, mostly around package acquisition:

* Downloading packages from the package source (network-bound).
* Verifying package hashes (CPU-bound).
* Calculating deltas between packages for delta compression (I/O-bound and CPU-bound).
* Uploading packages to deployment targets (network-bound).
* Monitoring deployment targets for job status, and collecting logs.

At some point, your server hardware is going to limit how many of these things a single Octopus Server can do concurrently. There are only so many CPUs you can allocate and only so many network connections which can be opened.  If a server overcommits itself and hits these limits, timeouts (network or SQL connections) will begin to occur, and deployments can begin to fail. Above all else, your deployments should be repeatable and reliable.

We offer three options for scaling your Octopus Server:

* Scale up by controlling the task cap and providing more server resources as required.
* Scale out using Octopus High Availability.
* Scale out using Workers.
* Scale out using Spaces. 

### Task Caps

An ideal situation would be an Octopus Server that's performing as many parallel deployments as it can while staying just under these limits. We tried several techniques to throttle Octopus Server automatically, but in practice, this kind of approach proved to be unreliable.

Instead, we decided to put this control into your hands, allowing you to control how many tasks each Octopus Server node will execute concurrently. This way, you can measure server metrics for your deployments, and then increase/decrease the task cap appropriately. Administrators can change the task cap in Configuration ➜ Nodes.

![](images/maintenance-changetaskcap.png)

The default task cap is set to 5 out of the box. Based on our load testing, this offered the best balance of throughput and stability for most scenarios.  The highest we'd recommend you set the task cap to is 20.  Anything more and you will start running into resource contention.

### Leverage Workers

Workers are a new feature added to Octopus Deploy in version 2018.7.x.  They allow you to offload several tasks typically done by the Octopus Deploy server onto a series of worker pools.  Please see the chapter on workers for more details.

### Octopus High Availability

You can scale out your Octopus Server by implementing a High Availability cluster. In addition to linearly increasing the performance of your cluster, you can perform certain kinds of maintenance on your Octopus Servers without incurring downtime.  We have written a chapter about High Availability; please see that for more details.

## Tips

Follow these tips to tune and maintain the performance of your Octopus:

1. Configure your Octopus Server and SQL Server on separate servers.
2. Avoid hosting your Octopus Server and its SQL Database on shared servers to prevent Octopus or the other applications from becoming "noisy neighbors".
3. Provide sufficient resources to your Octopus Server and SQL Server. Measure resource utilization during typical and/or heavy load, and decide whether you need to provision more resources.
   * We don't provide a one-size-fits-all set of specifications - your resource requirements will vary heavily depending on your specific scenario. The best way to determine what "sufficient resources" means, is to measure then adjust until you are satisfied.
4. Configure short retention policies. Less history == faster Octopus.
5. Maintain your SQL Server.
   * See above.
   * Upgrade to the latest version of Octopus Server.
   * Quite often negative performance symptoms are caused by outdated statistics or other common SQL Server maintenance problems.
6. If you have saturated your current servers you may want to consider scaling up, by increasing the resources available to the Octopus and SQL Servers, or scaling out:
   * Consider Octopus High Availability if you are reaching saturation on your current infrastructure, or want to improve the uptime of your Octopus Server, especially across Operating System patches. Octopus High Availability is designed to scale linearly as you add nodes to your cluster.
   * Consider using Workers and worker pools if deployment load is affecting your server. See this blog post for a way to begin looking at workers for performance.
   * Consider separating your teams/projects into "spaces" using the upcoming Spaces feature.
7. Try not to do too much work in parallel, especially without thorough testing. Performing lots of deployment tasks in parallel can be a false economy more often than not:
   * You can configure how many tasks from the task queue will run at the same time on any given Octopus Server node by going to Configuration ➜ Nodes. The default task cap is 5 (safe-by-default). You can increase this cap to push your Octopus to work harder.
   * Learn about tuning your deployment processes for performance.
8. Consider how you transfer your packages:
   * If network bandwidth is the limiting factor, consider using delta compression for package transfers.
   * If network bandwidth is not a limiting factor, consider using a custom package feed close to your deployment targets, and download the packages directly on the agent. This alleviates a lot of resource contention on the Octopus Server.
   * If Octopus Server CPU and disk IOPS is a limiting factor, avoid using delta compression for package transfers. Instead, consider downloading the packages directly on the agent. This alleviates a lot of resource contention on the Octopus Server.
9. Consider the size of your packages:
   * Larger packages require more network bandwidth to transfer to your deployment targets.
   * When using delta compression for package transfers, larger packages require more CPU and disk IOPS on the Octopus Server to calculate deltas - this is a tradeoff you can determine through testing.
10. Consider the size of your Task Logs:
   * Larger task logs put the entire Octopus pipeline under more pressure.
   * We recommend printing messages required to understand progress and deployment failures. The rest of the information should be streamed to a file, then published as a deployment artifact.
11. Prefer Listening Tentacles or SSH instead of Polling Tentacles wherever possible:
   * Listening Tentacles and SSH place the Octopus Server under less load.
   * We try to make Polling Tentacles as efficient as possible, but by their very nature, they can place the Octopus Server under high load just handling the incoming connections.
12. Reduce the frequency and complexity of automated health checks using machine policies.
13. Disable automatic indexing of the built-in package repository if not required.

## Troubleshooting Common Issues

The best place to start troubleshooting your Octopus Server is to inspect the Octopus Server logs. Octopus writes details for common causes of performance problems:

1. Request took 5123ms: GET {correlation-id}: If HTTP requests are taking a long time to be fulfilled, you'll see a message like this. The timer is started when the request is first received, ending when the response is sent.
Look for trends as to which requests are taking a long time.
Look to see if the performance problem occurs, and goes away, regularly. This can indicate another process hogging resources periodically.
2. The dashboard or project overview is taking a long time to load: long retention policies usually cause this. Consider tightening up your retention policies to keep fewer releases. It can also be caused by the sheer number of projects you are using to model your deployments.
3. {Insert/Delete/Update/Reader} took 8123ms in transaction '{transaction-name}': If a particular database operation takes a long time you'll see a message like this. The timer is started when the operation starts, ending when the operation is completed (including any retries for transient failure recovery).
   * If you are seeing these operations take a long time it indicates your SQL Server is struggling under load, or your network connection from Octopus to SQL Server is saturated.
   * Check the maintenance plan for your SQL Server. See the tips above.
   * Test a straightforward query like SELECT * FROM OctopusServerNode. If this query is slow, it indicates a problem with your SQL Server.
   * Test a more complex query like SELECT * FROM Release ORDER BY Assembled DESC. If this query is slow, it indicates a problem with your SQL Server or the sheer number of Releases you are retaining.
   * Check the network throughput between the Octopus Server and SQL Server by trying a larger query like SELECT * FROM Events.
4. Task Logs are taking a long time to load, or your deployments are taking a long time: The size of your task logs might be to blame.
See the tips above.
Make sure the disks used by your Octopus Server have sufficient throughput/IOPS available for processing the demand required by your scenario. Task logs are written and read directly from disk.
5. If you are experiencing overly high CPU or memory usage during deployments which may be causing your deployments to become unreliable:
   * Try reducing your Task Cap back towards the default of 5 and then increase progressively until your server is reliable again.
   * Look for potential performance problems in your deployment processes, especially:
   * Consider how you transfer your packages.
   * Consider reducing the amount of parallelism in your deployments by reducing the number of steps you run in parallel or the number of machines you deploy to in parallel.
6. System.InvalidOperationException: Timeout expired. The timeout period elapsed prior to obtaining a connection from the pool. This may have occurred because all pooled connections were in use and max pool size was reached.: This error indicates two possible scenarios:
   * Your SQL Queries are taking a long time, exhausting the SQL Connection Pool. Investigate what might be making your SQL Queries take longer than they should and fix that if possible - see earlier troubleshooting points.
   * If your SQL Query performance is fine, and your SQL Server is running well below its capacity, perhaps your Octopus Server is under high load. This is perfectly normal in many situations at scale. If your SQL Server can handle more load from Octopus, you can increase the SQL Connection Pool size of your Octopus Server node(s). This will increase the number of active connections Octopus is allowed to open against your SQL Server at any point in time, effectively allowing your Octopus Server to handle more concurrent requests. Try increasing the Max Pool Size in your SQL Connection String in the Octopus.Server.config file to something like 200 (the default is 100) and see how everything performs. Learn about Connection Strings and Max Pool Size.
   * Octopus is leaking SQL Connections. This should be very rare but has happened in the past, and we fix every instance we find. We recommend upgrading to the latest version of Octopus and get help from us if the problem persists.


## Don't be afraid to get in touch!

If none of these troubleshooting steps work, please get in contact with our support team and send along the following details (feel free to ignore points if they don't apply):

1. Are you running Octopus as an HA cluster or single node?
2. Is the SQL Database Server on the same machine as Octopus or a different machine?
3. Are you hosting any other applications on the same machine as Octopus or its SQL database?
4. What kind of server specs are you running for Octopus and SQL Server?
5. Approximately how many users do you have using Octopus?
6. Approximately how many projects and machines do you have?
7. Approximately how many deployments do you perform at the same time?
8. Do you notice any correlation between deployments of specific projects and the performance problem?
9. Do you notice any correlation between other Octopus Server tasks (like package retention policy processing) and the performance problem?
10. Does the Octopus Server ever become unresponsive and how frequently does it become unresponsive?
11. Does the Octopus Server recover after the performance degrades, or does it need to be manually restarted to recover?

In addition to answering those questions, please collect and attach the following diagnostics to your support request (probably the most important part):

1. Attach a screen recording showing the performance problem.
2. Attach any charts showing the Octopus Server performance (CPU/RAM) for normal deployment workloads both before/after the performance problem started.
3. Record and attach the performance problem occurring in your web browser (if applicable).
4. Attach the Octopus Server logs.
5. Attach the raw task logs for any tasks exhibiting the performance problem, or that may have been running at the same time as the performance problem.
6. If the performance problem is causing high CPU utilization on the Octopus Server, please record and attach a performance trace.
7. If the performance problem is causing high memory utilization on the Octopus Server, please record and attach a memory trace.

Please email mailto:Support@Octopus.com

## Conclusion

Regular maintenance and keeping Octopus Deploy up to date will go a long way in ensuring your experience with Octopus Deploy is positive.  If you do start to see a dip in performance please reach out to us.  We will do our best to help out.  We want your experience with Octopus Deploy to be great.