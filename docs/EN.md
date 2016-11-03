# Mediastream Platform SDK iOS

## Overview

This library allows you to embed and control your VOD and Live Stream player on your iOS application.

# Usage

## Getting started

First you need to add the library in your dependencies. The easiest way to do it is to install it using Carthage (https://github.com/Carthage/Carthage).
Once Carthage is already installed, in the root directory of your app, create a file called Cartfile with the following content:

```
github "mediastream/PlatformSDKiOS"
```

Then run the command:

```
carthage update
```

Make sure to enable the option 'App Transport Security' in your app to allow it to make HTTP requests

![alt tag](disable_app_transport_security.png)

After doing this, you can start with this basic example of usage:

```swift
import UIKit
import MediastreamPlatformSDK

class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()

        let playerConfig = MediastreamPlayerConfig()
        let mdstrm = MediastreamPlatformSDK()

        mdstrm.events.listenTo(eventName: "play", action: {
            NSLog("Player is playing")
        })
        
        mdstrm.events.listenTo(eventName: "pause", action: {
            NSLog("Player is paused")
        })
        
        mdstrm.events.listenTo(eventName: "error", action: { (information: Any?) in
            if (information != nil) {
                if let info = information as? String {
                    NSLog("ERROR: \(info)")
                }
            }
        })

        playerConfig.type = MediastreamPlayerConfig.VideoTypes.VOD
        playerConfig.id = "567317b15050c6e76e896864"

        self.addChildViewController(mdstrm)
        self.view.addSubview(mdstrm.view)

        mdstrm.setup(playerConfig)
        mdstrm.play()
    }

    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
}
```

## MediastreamPlayerConfig Class

This class holds the configurations for the MediastreamPlatformSDK.

### Properties

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| id | String | Yes | Video ID |
| type | MediastreamPlayerConfig.VideoTypes | YES | Video Type. posible values: `VOD`, `LIVE` |
| environment | MediastreamPlayerConfig.Environments | No | Envirinment where the video is located, `PRODUCTION` or `QA`. Default: `PRODUCTION` |
| adUrl | String | No | AdURL (e.g. VAST). Optional, if not specified will play ads configured in Mediastream Platform. |
| accessToken | String | No | Access token for restricted videos. |
| volume | Int | No | Initial volume. |

### Methods

| Return | Method | Description |
| --- | --- | --- |
| void | addAdCustomAttribute(key: String, value: String) | Add a custom attribute to the Ad. It applys only if an adUrl was passed |

### Contructors

MediastreamPlayerConfig()

## MediastreamPlatformSDK Class

Mediastream player for Live or VOD from Mediastream Platform. Includes ads from VAST.

### Properties

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| volume | Int | No | Changes the video volume and return the current value |
| currentTime | Double | No | Return the current position of the video. If a value is provided the player will jump to that position |

### Method

| Return | Method | Description |
| --- | --- | --- |
| void | setup(config: MediastreamPlayerConfig) | Configure the player. |
| void | play() | Start playing the video |
| void | pause() | Pauses the vídeo. |
| void | stop() | Stop playing the video. Same as pause() |
| void | seekTo(Double: position) | Go to the specified position in the video. |

### Events

MediastreamPlatoformSDK provides a build in event handler for capturing different events that may ocurr during the video reproduction

# Usage
```
let mdstrm = MediastreamPlatformSDK()
let events = mdstrm.events

mdstrm.events.listenTo(eventName: "play", action: {
    NSLog("Player is playing")
})

mdstrm.events.listenTo(eventName: "pause", action: {
    NSLog("Player is paused")
})

mdstrm.events.listenTo(eventName: "error", action: { (information: Any?) in
    if (information != nil) {
        if let info = information as? String {
            NSLog("ERROR: \(info)")
        }
    }
})
```
Available events

* play: Triggers whenever the video starts playing
* pause: Triggers whenever the video stops playing
* error: Triggers whenever there is an error with the reproduction


### Constructors

MediastreamPlatformSDK()

## Enum MediastreamPlayerConfig.Environments

### Options

`PRODUCTION`, `QA`

## Enum MediastreamPlayerConfig.VideoTypes

### Options

`LIVE`, `VOD`
