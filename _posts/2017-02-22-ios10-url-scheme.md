---
layout: post
title: IOS 10 Setting URL
---


####内容

导入MobileCoreServices.framework框架

```objc
#import <MobileCoreServices/MobileCoreServices.h>

// Prefs:root=WIFI P要大写

NSURL *url = [NSURL URLWithString:@"Prefs:root=WIFI"];

Class LSApplicationWorkspace = NSClassFromString(@"LSApplicationWorkspace");

[[LSApplicationWorkspace performSelector:@selector(defaultWorkspace)] performSelector:@selector(openSensitiveURL:withOptions:) withObject:url withObject:nil];
```


具体可用路径如下：

* prefs:root=General&path=About
* prefs:root=General&path=ACCESSIBILITY
* prefs:root=AIRPLANE_MODE
* prefs:root=General&path=AUTOLOCK
* prefs:root=General&path=USAGE/CELLULAR_USAGE
* prefs:root=Brightness
* prefs:root=General&path=Bluetooth
* prefs:root=General&path=DATE_AND_TIME
* prefs:root=FACETIME
* prefs:root=General
* prefs:root=General&path=Keyboard
* prefs:root=CASTLE
* prefs:root=CASTLE&path=STORAGE_AND_BACKUP
* prefs:root=General&path=INTERNATIONAL
* prefs:root=LOCATION_SERVICES
* prefs:root=ACCOUNT_SETTINGS
* prefs:root=MUSIC
* prefs:root=MUSIC&path=EQ
* prefs:root=MUSIC&path=VolumeLimit
* prefs:root=General&path=Network
* prefs:root=NIKE_PLUS_IPOD
* prefs:root=NOTES
* prefs:root=NOTIFICATIONS_ID
* prefs:root=Phone
* prefs:root=Photos
* prefs:root=General&path=ManagedConfigurationList
* prefs:root=General&path=Reset
* prefs:root=Sounds&path=Ringtone
* prefs:root=Safari
* prefs:root=General&path=Assistant
* prefs:root=Sounds
* prefs:root=General&path=SOFTWARE_UPDATE_LINK
* prefs:root=STORE
* prefs:root=TWITTER
* prefs:root=General&path=USAGE
* prefs:root=VIDEO
* prefs:root=General&path=Network/VPN
* prefs:root=Wallpaper
* prefs:root=WIFI
* prefs:root=INTERNET_TETHERING