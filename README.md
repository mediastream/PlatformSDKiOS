# Mediastream Platform SDK iOS

## Overview

This library allows you to embed and control your VOD and Live Stream player on your iOS application.

# Usage

## Getting started

First you need to add the library in your dependencies. The easiest way to do it is using our cocoapods library:

```
pod 'MediastreamPlatformSDKiOS', '1.0.70'
```

Don't forget to select "YES" in "Allow Arbitrary Loads", which is inside the "App Transport Security" property. Also, allow "Required Background mode" by selecting the option as in the image.

![alt tag](files/disable_app_transport_security_and_required_background_modes.png)


### Example
After doing this, you can [Donwload a Sample](files/sample.zip) or you can start with this basic code example of usage:

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

        playerConfig.accountID = "577beb007f565cf33ae87fa8" // AccoundID is now REQUIRED
        playerConfig.type = MediastreamPlayerConfig.VideoTypes.VOD
        playerConfig.id = "567317b15050c6e76e896864"

        //If you're using protected files you need add:
        //Url to get certificate needed to play protected files
        playerConfig.appCertificateUrl = "https://s3.amazonaws.com/streammanagerqa/player/drm/55117baedc01616019533551.cer"

        //Url to get license needed to play protected files
        playerConfig.drmUrl = "http://drm-fairplay-licensing.axtest.net/AcquireLicense"

        //Headers with the token of the file requested, this headers are needed to complement the main request with the drm license and certificate
        playerConfig.addDrmHeader("X-AxDRM-Message", value: "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ2ZXJzaW9uIjoxLCJjb21fa2V5X2lkIjoiMjdGNkNDMTEtODRGMS00MzFELTk5MDItQTZDODAwRUM4NzBCIiwibWVzc2FnZSI6eyJ0eXBlIjoiZW50aXRsZW1lbnRfbWVzc2FnZSIsImV4cGlyYXRpb25fZGF0ZSI6IjIwMTctMDUtMDFUMDc6NDU6MDIuMjM1WiIsImtleXMiOlt7ImlkIjoiYzUwODUzNjctNzA4Ni00OGFlLTgwN2MtNDMzZDhlYzBkYzRlIiwiaXYiOiIrWU91SG9wOEdoMVdHY0x1VUYvV3V3PT0ifV19fQ.u4uneeLvwWhOtvotvMBTQY2ymdNt-zr_OYiKkViXGd0")

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
| type | MediastreamPlayerConfig.VideoTypes | YES | Video Type. posible values: `VOD`, `LIVE`, `EPISODE` |
| environment | MediastreamPlayerConfig.Environments | No | Envirinment where the video is located, `PRODUCTION` or `DEV`. Default: `PRODUCTION` |
| adUrl | String | No | AdURL (e.g. VAST). Optional, if not specified will play ads configured in Mediastream Platform. |
| accessToken | String | No | Access token for restricted videos. |
| volume | Int | No | Initial volume. |
| drmUrl | String | No | Url to get license to play protected files with drm. |
| appCertificateUrl | String | No | Url to download the certificate neccesary to complete the request to play protectes files.|
| customerID | String | No | Unique Identification string for the video consumer customer.|
| dvr | boolean | No | Player starts prepared to use dvr, to use you need to set a windowDRV. Default: false |
| windowDVR | int | No | Window dvr voiced in seconds.|
| showControls | boolean | No | Hide the controls of the player. Default: true |
| referer | string | No | Allows set a custom referrer to find in the statistics. |
| customUI | boolean | No | Show a custom mediastream UI for AVPlayer. Default: false |
| loadNextAutomatically | boolean | No | Play next episode if exist (Only available if type EPISODE is setted). Default: false |
| videoFormat | MediastreamPlayerConfig.AudioVideoFormat | No | Video/Audio format available to play a source, `HLS`, `M4A`. Default: `HLS` |


### Methods

| Return | Method | Description |
| --- | --- | --- |
| void | addAdCustomAttribute(key: String, value: String) | Add a custom attribute to the Ad. It applys only if an adUrl was passed |
| void | addDrmHeader(key: String, value: String) | Allows to add headers necessary to complete the request to get the license to play protected files. |

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
| void | play() | Start playing the video. |
| void | pause() | Pauses the video. |
| void | stop() | Stop playing the video. Same as pause(). |
| void | seekTo(Double: position) | Go to the specified position in the video. |
| void | reloadPlayer(MediastreamPlayerConfig config) | Allows to reload the player with a new content without kill the player instance |
| void | releasePlayer() | This method is used to destroy the actual player, as a recommendation it's preferable to call it in the "deinit()" event of your view. |

### Events

MediastreamPlatoformSDK provides a build in event handler for capturing different events that may ocurr during the video reproduction

### Example

```swift
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
```
Available events

* play: Triggers whenever the video starts playing
* pause: Triggers whenever the video stops playing
* error: Triggers whenever there is an error with the reproduction
* onFullScreen: Triggers whenever the video is on Fullscreen mode
* offFullScreen: Triggers whenever the video is not on Fullscreen mode
* conectionStablished: Triggers whenever the SDK is connected to internet
* conectionLost: Triggers whenever the SDK lost internet connection
* finish: Triggers whenever the video ends playing
* ready: Triggered when the player is ready to play video
* buffering: Triggered when player enters buffering state
* seek: Triggered when the user is seeking

### Constructors

MediastreamPlatformSDK()

## Enum MediastreamPlayerConfig.Environments

### Options

`PRODUCTION`, `DEV`

## Enum MediastreamPlayerConfig.VideoTypes

### Options

`LIVE`, `VOD`, `EPISODE`

## License

MediastreamPlatformSDK is available under the MIT license.
