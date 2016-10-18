# Samsung Development Guide for Web Apps

TV Apps are created as standard HTML5 apps. You can create your TV app using the TizenTV IDE ([Tizen SDK](https://developer.tizen.org/development/tools/download)), or any editor that you choose.

Apps are usually created as SPA. You can use most frameworks and libraries used in web based application development including jQuery, Backbone, Angular, React, and others.

TV apps are displayed on the TV fullscreen, without any browser controls. The window size will vary depending on the resolution of the TV. But typically this means you will only have to deal with 2 different sizes: [1920x1080 or 1280x720](https://www.samsungdforum.com/Tizen/Spec#GeneralFeatures).

###Essentials of Developing Tizen Web Application

####SDK
To develop Tizen TV web apps, you need to install a SDK. You can download a SDK from Samsung Developer Forum site:
[Tizen SDK Download](https://developer.tizen.org/development/tools/download)

To use a GPU accelerator you should install Intel HAXM(Hardware Accelerated Execution), otherwise an emulator will not work properly. During installing a SDK, Intel HAXM should be installed automatically. In case of any errors you can install manually the following file: [HAXM](https://www.samsungdforum.com/guide_static/tizenoverviewguide/_downloads/IntelHaxmTizen_mac.zip).

> The HAXM driver is automatically installed with the IDE, only install it if the emulator does not work.

Emulator runs on a virtual machine (QEMU), none of these are supported (and the Emulator will complain/not work if they are running on the host computer):
	* Virtual Box
	* VMWare
	* Parallels(Mac PC)
####Generating Author Certificate

All apps must be signed with an author certificate. It is used to identify an app developer and protect apps from mixing and misusing. It is registered by a SDK installed on each PC. The following is the guide of registering.

_Follow this guide to generate the certificate:_
[https://www.samsungdforum.com/TizenGuide/tizen3531/index.html](https://www.samsungdforum.com/TizenGuide/tizen3531/index.html)

After creating the author certificate, be careful not to lose it. This is the unique proof for certifying app developer. If you lose it, you can’t version up your app.

####UI/UX Requirements
Samsung Smart TV provides a user experience that differs completely from that of other familiar devices, such as mobile devices and desktop computers.

#####General Considerations:
![alt text][tv]
[tv]: https://www.samsungdforum.com/guide_static/tizenuxguide/img_01_01_tv_viewing.png "TV Use"

* The average distance between a TV and its viewers is 3 meters (10ft).
* The standard remote control is the main input method.
* TV is used by more than one person.
* Simplicity, Clarity, User Control, ConsistencyFeedback, Feedback and Aesthetic Considerations should be taken into consideration when developing the app.

> [More on UI/UX Guidelines. ](https://www.samsungdforum.com/TizenUxGuide/)

####App Resolution
On Samsung UHD TV, the standard of app resolution is 1920x1080px and the aspect ratio is 16:9. (In case of Samsung FHD TV, it is 1280x720px.) Even if the resolution is different from the standard, the aspect ratio should be kept. Only if you keep the ratio, whitespace and scrollbars will not appear on a screen when the app is scaled up or down.

####APIs
Information about the APIs available [here](https://www.samsungdforum.com/TizenApiGuide/).

####Browser User-Agent String Format

| Year | UA String |
| :--: | :------- |
| 2015 | Mozilla/5.0 (SMART-TV; Linux; Tizen 2.3) AppleWebkit/538.1 (KHTML, like Gecko) SamsungBrowser/1.0 TV Safari/538.1 |
| 2014 | Mozilla/5.0 (SMART-TV; X11; Linux armv7l) AppleWebkit/537.42 (KHTML, like Gecko) Safari/537.42 |
| 2013 | Mozilla/5.0 (SMART-TV;X11; Linux i686) AppleWebkit/535.20+ (KHTML, like Gecko) Version/5.0 Safari/535.20+ |
| 2012 | Mozilla/5.0 (SMART-TV; X11; Linux i686) AppleWebKit/534.7 (KHTML, like Gecko) Version/5.0 Safari/534.7 |
| 2011 | Mozilla/5.0 (SmartHub; SMART-TV; U; Linux/SmartTV) AppleWebKit/531.2 (KHTML, like Gecko) Web Browser/1.0 SmartTV Safari/531.2+ |

@source: [https://github.com/ruiposse/smart-tv-app-dev-guidelines](https://github.com/ruiposse/smart-tv-app-dev-guidelines)

@source: [http://developer.samsung.com/technical-doc/view.do?v=T000000203](http://developer.samsung.com/technical-doc/view.do?v=T000000203)

###App Development

####Registering Remote Controller Key
An app should controlled by the _KeyCode_ of a remote controller. Only if you register the key to use, an app can receive the KeyCode.

Key codes can be registered with the Tizen API as follows:
```javascript
tizen.tvinputdevice.registerKey(KeyName);
```

Getting supported keys:
```javascript

var value = tizen.tvinputdevice.getSupportedKeys();
console.log(value);
```

Using key codes requires the following privilege on the config.xml file.
```xml
<tizen:privilege name='http://tizen.org/privilege/tv.inputdevice'/>
```

> [More on TVInputDevice API.](https://www.samsungdforum.com/tizenapiguide/tizen3331/index.html)

> [More on _privileges_.](https://www.samsungdforum.com/TizenGuide/tizen3431/index.html)

####APIs for VOD Service App
1. There are two ways to play VODs on an app. One way is using the video tag, which is an HTML5 standard element. And the other way is by using __webapis.avplay__ which is an API supported by Samsung Tizen TV. More details bellow:

  1.1. __Video Tag__: Video tag makes VODs playing easy by a HTML5 standard element. If you can enough develop by using only it, we recommend using it rather than webapis.avplay

  1.2. __webapis.avplay__: The following is the list to be difficult or not supported by video tag. When you use these technologies, you can use webapi.avplay.

* DRM(Digital Rights Management)
* HLS(HTTP Live Streaming)
* DASH(Dynamic Adaptive Streaming over HTTP)
* Smooth Streaming
* Adaptive Streaming
* 3D Contents, Closed Caption etc…

> [More on AVPlay API](https://www.samsungdforum.com/tizenapiguide/tizen3001/index.html)


> [Official Guide Overview PDF](https://www.samsungdforum.com/guide_static/tizenoverviewguide/_downloads/Essentials_of_Developing_Tizen_Web_Application_EN_1_4(1).pdf)

###AVPlay API

####AVPlay Object Lifecycle

![alt text][cycle]
[cycle]: https://www.samsungdforum.com/guide_static/tizenguide/_images/AVPlay_state.jpg "AVPlay Object Lifecycle"

####Adaptive Streaming
AVPlay module of Samsung Tizen TV supports adaptive streaming playing. DASH, HLS, Smooth Streaming are supported in Samsung Tizen TV. It enables user to change bitrate during playback.

The adaptive streaming engine needs to be specified when __open()__ API is called. Based on media file extension name, adaptive streaming engine is adjusted.

__Smooth Streaming__
.ism/Manifest

__DASH__
.xml
.mpd

__HLS__
.m3u8

__Widevine__
.wvm
.vob

####Example of object initialization (Call order is very important.)

```javascript
videoPlay: function (url) {
    var listener = {
        onbufferingstart: function () {
            console.log("Buffering start.");
        },
        onbufferingprogress: function (percent) {
            console.log("Buffering progress data : " + percent);
        },
        onbufferingcomplete: function () {
            console.log("Buffering complete.");
        },
        oncurrentplaytime: function (currentTime) {
            console.log("Current playtime: " + currentTime);
        },
        onevent: function (eventType, eventData) {
            console.log("event type: " + eventType + ", data: " + eventData);
        },
        ondrmevent: function (drmEvent, drmData) {
            console.log("DRM callback: " + drmEvent + ", data: " + drmData);
        },
        onstreamcompleted: function () {
            console.log("Stream Completed");
            webapis.avplay.stop();
        },
        onerror: function (eventType) {
            console.log("event type error : " + eventType);
        }
    };

    webapis.avplay.open(url);
    webapis.avplay.setDisplayRect(0, 0, 1920, 1080);
    webapis.avplay.setListener(listener);

    var drmParam = {
      DeleteLicenseAfterUse : true
    };

    //drmParam.LicenseServer = "license server url to play content";
    //drmParam.CustomData = "Custom Data to play content";
    webapis.avplay.setDrm("PLAYREADY", "SetProperties", JSON.stringify(drmParam));

    webapis.avplay.prepare();
    webapis.avplay.play();
}
```

To adjust the properties of the streaming you can use __webapis.avplay.setStreamingProperty()__.

```javascript
webapis.avplay.setStreamingProperty("ADAPTIVE_INFO", params);
```

This method should be called only on "IDLE" state.

To configure DRM playback, the method __webapis.avplay.setDrm()__ can be used.

This method updates DRM information, such as SetProperties etc. It changes the DRM mode, and runs the Control Feature. Every DRM has difference between AVPlayDrmOperation and jsonParam.

```javascript
var params = {
  LicenseServer: 'URL TO LICENSE SERVER',
  DeleteLicenseAfterUse: true
};

try {
    webapis.avplay.setDrm("PLAYREADY", "SetProperties", JSON.stringify(params));
} catch (e) {
    //Error
}
```

This method should be called on these states - "IDLE" and "PAUSED".


>[setStreamingProperty()](https://www.samsungdforum.com/TizenApiGuide/tizen3001/index.html#AVPlay-AVPlayManager-setStreamingProperty)

>[AVPlay Official Guide](https://www.samsungdforum.com/TizenGuide/tizen3451/index.html)

###CLI Tizen SDK

__Export SDK path__
```bash
export PATH=$PATH:/Users/keiverh/tizen-sdk/tools/ide/bin
```

__Setup global profile__
```bash
tizen cli-config -g default.profiles.path=/Users/keiverh/workspace/.metadata/.plugins/org.tizen.common.sign/profiles.xml
```

> This profile is created using the Tizen IDE. It identifies the developer and distributor certificate.

__List platforms and templates__
```bash
tizen list web-project
```

__Create project__
```bash
tizen create web-project -p  tv-2.4 -t WebBasicapplication -n blimcli -- /Users/keiverh/workspace
```

__Build package__
```bash
tizen build-web -- ~/workspace/blimcli
```

__Package app__
```bash
tizen package --type wgt --sign dev -- /Users/keiverh/workspace/blimcli/.buildResult
```

__Install package__
```bash
tizen install --target emulator-26101 --name blimcli.wgt -- /Users/keiverh/workspace/blimcli/.buildResult
```
___
> ###Online Resources
>- [App Structure](https://www.samsungdforum.com/TizenOverview#contents)
>- [Platform Features](https://www.samsungdforum.com/Tizen/Spec#GeneralFeatures)
>- [W3C/HTML5 API Reference](https://www.samsungdforum.com/TizenApiGuide/tizen851/index.html)
>- [Setting Project Properties ](https://developer.tizen.org/ko/development/getting-started/web-application/application-development-process/setting-project-properties?langredirect=1#set_widget)
>- [Examples on GitHub](https://github.com/Samsung/TizenTVApps)
>- [APIs Reference](https://developer.tizen.org/development/api-references/web-application)
>- [AVPlay Reference](https://www.samsungdforum.com/tizenapiguide/tizen3001/index.html)
>- [Caph - Focus handling for Angular and jQuery](https://www.samsungdforum.com/AddLibrary/CaphSdkDownload)
>- [App Distribution Guide](http://www.samsungdforum.com/Support/Distribution)
>- [CLI Dev Tools](https://developer.tizen.org/development/tools/web-tools/command-line-interface)
>- [Tizen Key Codes](https://www.samsungdforum.com/TizenGuide/tizen3551/index.html)
>- [Developing for Tizen TV - Article](http://clearbridgemobile.com/lessons-learned-developing-for-tizen-tv/)


####Caph

CAPH is a Web UI framework for TVs, it has these modules:

- Key Navigation
- Scrollable List and Grid
- UI Components : Button, Radio Button, Toggle Button, Checkbox, Input, Dialog, Context Menu, Dropdown Menu
- Touch feature : Pan, Tap and Double Tap. (from CAPH 3.1)

Helps developers handle user input from four directional keys on the remote and maximizes the usage of the GPU, since TV devices generally have a far more powerful GPU than CPU.

> It is not open source and only has versions for jQuery and Angular 1.x.

[Caph Documentation](https://www.samsungdforum.com/caphdocs/)
