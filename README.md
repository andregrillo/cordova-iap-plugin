# Cordova InApp Purchase Plugin

> In-App Purchases for Cordova

## Summary

This plugin allows **In-App Purchases** to be made from **Cordova** applications.

It lets you handle in-app purchases on iOS.


## Installation

### Install the plugin

```sh
cordova plugin add https://github.com/andregrillo/cordova-iap-plugin.git
```

### Install recommended plugins

<details>
<summary>
Install <strong>cordova-plugin-network-information</strong> (click for details).
</summary>


Sometimes, the plugin cannot connect to the app store because it has no network connection. It will then retry either:

* periodically after a certain amount of time;
* when the device fires an ['online'](https://developer.mozilla.org/en-US/docs/Web/Events/online) event.

The [cordova-plugin-network-information](https://github.com/apache/cordova-plugin-network-information) plugin is required in order for the `'online'` event to be properly received in the Cordova application. Without it, this plugin will only be able to use the periodic check to determine if the device is back online.

</details>

### Usage

Initializes the plugin
It takes a "products" option, which is an array containing products IDs and duration of the subscription in seconds.
```js
nonRenewing.initialize({
    products: [{
        id: 'com.outsystemsenterprise.biolab.destress.dev.2months',
        duration: 5184000
    }]
});
```

Opens the subscription manager:
```js
nonRenewing.openSubscriptionManager();
``` 

To know if the user is subscribed:
```js
nonRenewing.getStatus(function(error, status) {
    if (error) {
        console.error("Failed to load subscription status: " + error);
        return;
    }
    console.log("Is Subscribed: " + status.subscriber);
    console.log("Expired: " + status.expired);
    console.log("Expiry Date: " + status.expiryDate);
});
```


Below some raw documentation about the methods we've seen so far:

#### nonRenewing.initialize(options)

Initialize the In-App Purchase plugins, load subscription status and the in-app products.

Available options:

 * `products` (required). An array of product. Each product has an `id` and a `duration` in seconds.
 * `verbosity`. Default to `store.INFO`. See the cordova purchase plugin for possible value.
 * `loadExpiryDate`. Default to a function that loads from localStorage. See the related section on how to load the subscription status on your server.
 * `saveExpiryDate`. Default to a function that saves to localStorage. See the related section on how to save the subscription status on your server.
 * `dialog`. Holds UI options for the In-App Purchase dialog.
   - `parent`. Default to `document.body`. Parent HTML element to which to attach the In-App Purchase dialog.

#### nonRenewing.openSubscriptionManager()

Opens a popover that shows the user the current subscription status, and options to renew it.

#### nonRenewing.getStatus(callback)

Will load the subscription status and return it.

The callback takes 2 arguments:

1. an error string (will be null if loading the subscription status succeeded)
2. a status object with the following fields:
  * `subscriber`: true if the user is a subscriber
  * `expiryDate`: human readable date (uses Javascript's `getLocaleDate()`)
  * `expired`: true if the user was a subscriber, but the expiry date has passed
  * `expiryTimestamp`: timestamp containing the expiry date (millseconds since 1970)

### Monitor change of subscription status

You can register listeners using the `nonRenewing.onStatusChange` method.

Here's an example of that.

```js
nonRenewing.onStatusChange(function(status) {
    if (!status) {
        console.log("Status is unknown");
    }
    else {
        console.log("Is Subscribed: " + status.subscriber);
        console.log("Expiry Date: " + status.expiryDate);
    }
});
```

The `status` parameter is the same as `getStatus`, it can be null when the status is not known.


### Handling login/logout events

When a user logs in or out, you will want to reset the expiry date cached by the extension.

To do so, just call: `nonRenewing.reset();`.
