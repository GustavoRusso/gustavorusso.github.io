---
layout: post
title:  "Redirecting to iOS Settings"
date:   2016-09-26 21:00:00 +0100
categories: blog
---
Most apps redirect to iOs settings in order to manage privacy permissions.

## Using the Settings Launch URL (iOS 8.0+)

Apple allows to integrate our app with other iOS apps by means of the [Apple URL Schemes](https://developer.apple.com/library/ios/featuredarticles/iPhoneURLScheme_Reference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40007899).
Each app defines it's owns list of URLs that can be used like shortcuts to its specific screens.

The URL to interact with the iOS preferences system is defined by the [UIApplicationOpenSettingsURLString](https://developer.apple.com/reference/uikit/uiapplicationopensettingsurlstring) constant. The method   [UIApplication.open()](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIApplication_Class/index.html#//apple_ref/doc/uid/TP40006728) allows to navigate to this URL.

We could use this code to launch the Settings app and display the settings bundle of our app:


~~~ swift
if let appSettings = URL(string: UIApplicationOpenSettingsURLString) {
  UIApplication.shared.open(appSettings, options: [:], completionHandler: nil)
}
~~~

In addition, iOS will show a back button to return to our app:

{% include figure.html fileName="2016-09-26-ios-back-to-app-button.png" description="Example of back button on the top-left corner of the screen" %}

{% include note.html caption="Note:" description="This redirection will fail if our app hasn't yet created its settings bundle. It could happen if we only use system settings to handle privacy. To avoid this crash, we must request the privacy permissions prior to redirect to settings. This will force iOS to create our app's custom setting." %}

Starting from iOS 10, this is the only way allowed to redirect to the iOS preferences system.

## We can't redirect like Apple does

Some Apple apps redirect to specific privacy settings. For example, Maps redirects to the Location Services screen:

{% include figure.html fileName="2016-09-26-ios-apps-can-navigate-to-specific-settings.png" description="The Maps app can redirect you directly to the Location Services screen" %}

Unfortunately we can’t do those redirections like Apple does.

The constant `UIApplicationOpenSettingsURLString`{:.language-swift} contains the value "app-settings:". This value looks like other URL Schemes root identifiers. The disadvantage is that this schema does not allows to append parameters in order to redirect to specific subsections of the Settings app.

On the past, the "prefs:" schema identifier was associated to the Settings app. This schema allowed to append paths to redirect to specific settings screens.
The "prefs:" schema was turned private starting from iOS 5.1.
With iOS 8, the schema become available again - even if there was no official documentation - but [with iOS 10 it has been definitively restricted](http://stackoverflow.com/questions/8246070/ios-launching-settings-restrictions-url-scheme).


It would be nice to have back the chance to navigate to concrete subscreens of the Settings app.
