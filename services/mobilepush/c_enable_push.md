---

copyright:
 years: 2015, 2016

---

# Enabling Push Notifications
{: #c_enable_push-notifications}
Last updated: 17 October 2016
{: .last-updated}

You can enable applications to receive push notifications to your devices.

This section describes how to enable your client applications - mobile, web browser applications and also Chrome Apps and Extensions to receive push notifications, how to create basic notifications, get and initialize the SDK or plug-in, and how to register your device or browser to receive push notifications. You can also enable your mobile and web browser applications to receive push notifications by using the [REST API](t_restapi.html).

Note: For device, browser, Chrome Apps and Extensions registrations, the {{site.data.keyword.mobilepushshort}} service maintains a unique reference to tokens issued from notification providers -
APNs for Apple or GCM for Google. The tokens can be invalidated by the {{site.data.keyword.mobilepushshort}} service notification provider for several reasons. 

For example, during un-installation of an app on the device. In such a scenario, when a notification is attempted to be delivered based on the providers response that the device is invalidated, the {{site.data.keyword.mobilepushshort}} service will remove the device or web browser registrations. This in turn would restrain consequent attempts in sending notification to those invalidated devices.
