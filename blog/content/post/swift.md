---
title: Swift
author: Bill Farmer
type: post
date: 2018-03-28T15:56:14Z
url: /swift/
categories:
  - Hacking
keywords:
  - mac
  - app
  - swift
  - osx
  - macosx
---

Some years ago I wrote an OSX app in C using [Carbon][1] because my
Hackintosh would only run [OSX 10.4 (Tiger)][2]. The most difficult
part was dealing with the [Core Audio][3] API.

I recently decided to port it to [Swift][4] to avoid having to attempt
to learn Objective C. A derivation of C that sends selectors to
targets using the syntax `[target selector arguments...]` I don't want
to know about.

### Core Audio

Having translated the Core Audio part of the app to Swift, I
discovered that part of it refused to work. The same code originally
written in C works fine.

```swift
        var id: AudioDeviceID = kAudioObjectUnknown

        // Get the default input device
        var inputDeviceAOPA: AudioObjectPropertyAddress =
          AudioObjectPropertyAddress(mSelector:
                                       kAudioHardwarePropertyDefaultInputDevice,
                                     mScope: kAudioObjectPropertyScopeGlobal,
                                     mElement:
                                       kAudioObjectPropertyElementMaster)

        var size: UInt32 = UInt32(MemoryLayout.size(ofValue: id))

        // Get device
        status =
          AudioObjectGetPropertyData(UInt32(kAudioObjectSystemObject),
                                     &inputDeviceAOPA,
                                     0, nil, &size, &id)
        if (status != noErr)
        {
	    // AudioObjectGetPropertyData
            NSLog("Error in AudioObjectGetPropertyData: " +
	            "kAudioHardwarePropertyDefaultInputDevice %d", status)
	    return status
        }

        NSLog("System input device %d", id)

        // Set the audio unit device
        status =
          AudioUnitSetProperty(output,
			       kAudioOutputUnitProperty_CurrentDevice, 
			       kAudioUnitScope_Global,
                               0, &id, size)
        if (status != noErr)
        {
            // AudioUnitSetProperty
            NSLog("Error in AudioUnitSetProperty: " +
	            "kAudioOutputUnitProperty_CurrentDevice " +
                    AudioUnitErrString(status))
	    return status
        }
```

Swift version. This fails to set the input device with an error.

```c
    AudioDeviceID id;
    size = sizeof(id);

    // Get the default input device
    AudioObjectPropertyAddress inputDeviceAOPA =
        {kAudioHardwarePropertyDefaultInputDevice,
         kAudioObjectPropertyScopeGlobal,
         kAudioObjectPropertyElementMaster};

    // Get device
    status = AudioObjectGetPropertyData(kAudioObjectSystemObject,
                                        &inputDeviceAOPA,
                                        0, nil, &size, &id);
    if (status != noErr)
    {
        // AudioObjectGetPropertyData
        NSLog(@"Error in AudioObjectGetPropertyData: " 
                    "kAudioHardwarePropertyDefaultInputDevice %s (%d)",
              AudioUnitErrString(status), status);
        return status;
    }

    // Set the audio unit device
    status = AudioUnitSetProperty(audioData.output,
                                  kAudioOutputUnitProperty_CurrentDevice, 
                                  kAudioUnitScope_Global,
                                  0, &id, size);
    if (status != noErr)
    {
        // AudioUnitSetProperty
        NSLog(@"Error in AudioUnitSetProperty: " 
                    "kAudioOutputUnitProperty_CurrentDevice %s (%d)",
              AudioUnitErrString(status), status);
        return status;
    }
```

C version, this works with no error. I asked a question about this on
the [coreaudio mailing list][5], but apart from being picked up on my
coarse use of Swift, since fixed, I didn't get an answer. So I decided
to leave the audio part of the app in C. I changed the file extension
to `.m` so that it is compiled as Objective C which allows a certain
amount of interoperability with Swift. In order for this to work, two
special include files need to be used. `<AppName>-Bridging-Header.h`
allows Swift to access C data. It can just include the C include
file. Use the switch `-import-objc-header` to import this header.

```C
#include "Audio.h"
```

To allow C to access Swift data requires the use of `<AppName>-Swift.h`, which is created by the Swift compiler using the switch `-emit-objc-header-path`.

```Makefile
HEADER = Tuner-Bridging-Header.h
SWIFTH = Tuner-Swift.h

SOURCES = AppDelegate.swift ScopeView.swift SpectrumView.swift \
		StrobeView.swift DisplayView.swift MeterView.swift \
		TunerView.swift Audio.o

SFLAGS = -g -target x86_64-apple-macosx10.9 \
	-import-objc-header $(HEADER) -emit-objc-header-path $(SWIFTH) \
	-Xlinker -rpath -Xlinker @loader_path/../Frameworks -Xlinker -w

CFLAGS = -g -target x86_64-apple-macosx10.9
```

Extract from `Makefile`.

### Enums

I make great use of [enums][6] in my apps. They are very useful for
defining lots of constants for use in case statements. You can use
enums in Swift, but the syntax is more complex, and using them in a
switch statement in Swift is a pain. I found it was easier to define
the enums in the C include file, and then use them in Swift.

```C
// Checkbox tags
enum
    {kZoom   = 'Zoom',
     kFilt   = 'Filt',
     kMult   = 'Mult',
     kFund   = 'Fund',
     kStrobe = 'Strb',
     kDown   = 'Down',
     kLock   = 'Lock',
     kNote   = 'Note'};

// Reference tags
enum
    {kRefText = 'RefT',
     kRefStep = 'RefS'};
```

The enums can then be referenced in Swift.

```swift
    // buttonClicked
    @objc func buttonClicked(sender: NSButton)
    {
        print("Sender", sender, sender.state)
        switch sender.tag
        {
        case kZoom :
            spectrumData.zoom = (sender.state == .on) ? true : false

        case kFilt :
            audioData.filt = (sender.state == .on) ? true : false

        case kMult :
            displayData.mult = (sender.state == .on) ? true : false
            displayView.needsDisplay = true

        case kFund :
            audioData.fund = (sender.state == .on) ? true : false

        case kStrobe :
            strobeData.enable = (sender.state == .on) ? true : false

        case kDown :
            audioData.down = (sender.state == .on) ? true : false

        case kLock :
            displayData.lock = (sender.state == .on) ? true : false
            displayView.needsDisplay = true

        case kNote :
            audioData.note = (sender.state == .on) ? true : false

        default:
            break
        }
    }
```

### Boolean arrays

The data defined in the include file includes two boolean arrays.

```C
// Audio filter
typedef struct
{
    bool note[12];
    bool octave[9];
} FilterData;
FilterData filterData;
```

These arrays appear as [tuples][9] in Swift, which is not very
useful. I found a workaround using reflection on [Stack Overflow][10],
but it produces the wrong result.

```swift
    // This function compiles and appears to work but produces the
    // wrong result for a boolean array. Replaced by ObjC access
    // functions.
    // arrayFromTuple
    func arrayFromTuple<T, R>(tuple: T) -> [R]
    {
        let reflection = Mirror(reflecting: tuple)
        var array: [R] = []
        for i in reflection.children
        {
            array.append(i.value as! R)
        }

        return array
    }
```

So I wrote some access functions in C as a workaround.

```C
// Boolean array access functions to work around Swift limitations
// getNote
bool getNote(int index)
{
    return filterData.note[index];
}

// setNote
void setNote(bool value, int index)
{
    filterData.note[index] = value;
}

// getOctave
bool getOctave(int index)
{
    return filterData.octave[index];
}

// setOctave
void setOctave(bool value, int index)
{
    filterData.octave[index] = value;
}
```

### Sliders

I need to use two [sliders][7] in another app I am porting. To define
the [orientation][8] of the slider, the docs say to use `isVertical`,
macOS 10.0+. But the compiler says macOS 10.12+ and I want to support
10.9. The docs for Objective C say `vertical`, but the compiler
doesn't like that either. So I wrote another access function.

```C
// setVertical
void setVertical(NSSlider *slider, bool value)
{
    slider.vertical = value;
}
```

 [1]: https://en.wikipedia.org/wiki/Carbon_(API)
 [2]: https://en.wikipedia.org/wiki/Mac_OS_X_Tiger
 [3]: https://developer.apple.com/documentation/coreaudio
 [4]: https://developer.apple.com/swift
 [5]: https://lists.apple.com/archives/coreaudio-api
 [6]: https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Enumerations.html
 [7]: https://developer.apple.com/documentation/appkit/nsslider
 [8]: https://developer.apple.com/documentation/appkit/nsslider/1527901-isvertical
 [9]: https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/TheBasics.html
 [10]: https://stackoverflow.com/questions/34776546/access-element-of-fixed-length-c-array-in-swift
