# Subscriptions

Who changed that project's deployment process? Who removed Bob from the administrator's group?

The Octopus audit log will tell you who did these actions which is useful after the fact, but wouldn't you rather know _when_ certain changes happen?

If you answered "yes," then you're in luck! This is exactly what the Subscriptions feature is for.

Let's create a subscription that will notify administrators whenever a production deployment starts. You can create an email step or Slack notification step in each project and scope it to the Production environment, but we can be notified about all production deployments with a single subscription.

## Creating Subscriptions

Navigate over to Configuration > Subscriptions to see any existing subscriptions.

Click the Add Subscription button to create a new subscription.

Let's give it a descriptive name like "Production Deployment."

We'll leave it enabled since we want it to be active after we create it.

### Event Filters

Now we need to create a filter that represents production deployments. The first drop down is the event group selection. Event groups are great if you want to filter to a known set of events. For example, _machine critical-events_ inclues _machine cleanup failed_ and _machine failed to be unavailable_. We'll leave this blank since we're only looking for a single event.

We want to know when a production deployment starts, so click on _select event categories_ and choose _deployment started_.

Describe document types.

Describe users.

Describe projects.

The last piece for the filter is the environment. Click _select environments_ and choose _Production_.

There are two options for how to deliver the notification, email or webhook.

### Email Notifications

We're going to set up this subscription with an email notification.

First, choose a team to receive the email. When the subscription fires, an email will be sent to each member of the team with the permissions to see that event.

Emails are not sent at the time of event but at the frequency we choose. The default is one hour, so every hour this subscription will be evaluated and a digest of all matching events will be sent out. One hour seems a bit long for monitoring production deployments, so let's turn that up to every five minutes.

Select your desired timezone and email priority. I'll choose High priority for my subscription.

### Webhook Notifications

You can also configure the subscription to send the event payload to a webhook. We won't be configuring a webhook in this chapter, but we will cover the settings available.

You will configure a url to an endpoint that will accept the subscription payload. The payload will be delivered in the body of a POST request using an application/json content type, contained in a parameter called "Payload". Each payload will contain a single event.

You can set an option header to be sent with the request.

By default, all events that match the filter will be sent to the webhook. You can limit this to events that match the permissions for one or more teams.

The last option is the timeout for the webhook request.

## Conclusion

In this chapter, we introduced subscriptions and configured a subscription that will send us email notifications when releases are deployed to production.