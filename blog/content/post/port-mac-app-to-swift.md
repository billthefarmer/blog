---
title: Port Mac OSX App to Swift
author: Bill Farmer
type: post
date: 2018-02-14T13:56:14Z
url: /port-mac-osx-app-to-swift/
categories:
  - Hacking
keywords:
  - mac
  - app
  - swift
---

Some years ago I wrote an OSX app in C using [Carbon][1] because my
Hackintosh would only run [OSX 10.4 (Tiger)][2]. The most difficult
part was dealing with the [Core Audio][3] API.

The app has several views: a scope view, a spectrum view, a display
view that shows the detailed output, a strobe view, a meter view and a
status bar. Using [AppKit][4] makes it relatively easy to make the app
dynamically resizeable. This is done using a [NSStackView][5].

```swift
        scope = Scope()
        spectrum = Spectrum()
        display = Display()
        strobe = Strobe()
        meter = Meter()
        status = Status()

        stack = NSStackView(views: [scope, spectrum, display,
                                    strobe, meter, status])

        let spectrumHeight = NSLayoutConstraint(item: spectrum,
                                                attribute: .height,
                                                relatedBy: .equal,
                                                toItem: scope,
                                                attribute: .height,
                                                multiplier: 1,
                                                constant: 0)
        // ...
        stack.addConstraint(spectrumHeight)
        stack.addConstraint(displayHeight)
        stack.addConstraint(strobeHeight)
        stack.addConstraint(meterHeight)
        stack.addConstraint(statusHeight)

        stack.orientation = .vertical
        stack.spacing = 8
        stack.edgeInsets = NSEdgeInsets(top: 20, left: 20,
                                        bottom: 20, right: 20)

        window.contentView = stack
```

The audio code is similar to the C version with extra declarations
needed to deal with the changes in the API and the swift requirement
for argument labels.

```C
    // Specify an output unit
    ComponentDescription dc =
	    {kAudioUnitType_Output,
        kAudioUnitSubType_HALOutput,
        kAudioUnitManufacturer_Apple,
        0, 0};

    // Find an output unit
    Component cp = FindNextComponent(NULL, &dc);
```
becomes
```swift
        // Specify an output unit
        var dc = AudioComponentDescription(componentType: kAudioUnitType_Output,
                                           componentSubType: kAudioUnitSubType_HALOutput,
                                           componentManufacturer: kAudioUnitManufacturer_Apple,
                                           componentFlags: 0,
                                           componentFlagsMask: 0);
        // Find an output unit
        let cp = AudioComponentFindNext(nil, &dc)
```
 [1]: https://en.wikipedia.org/wiki/Carbon_(API)
 [2]: https://en.wikipedia.org/wiki/Mac_OS_X_Tiger
 [3]: https://developer.apple.com/documentation/coreaudio
 [4]: https://developer.apple.com/documentation/appkit
 [5]: https://developer.apple.com/documentation/appkit/nsstackview
