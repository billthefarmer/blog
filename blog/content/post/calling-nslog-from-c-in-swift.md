---
title: Calling NSLog from C in Swift App
author: Bill Farmer
date: 2018-02-18T16:10:25Z
url: /calling-nslog-from-c-in-swift/
categories:
  - Hacking
keywords:
  - OSX
  - MacOS
  - App
  - Swift
  - C
  - NSLog
  - CoreAudio
  - CoreFoundation
---

I have had to write part of a [Swift][0] app dealing with
[CoreAudio][1] for OSX in C because I couldn't get it to work in
Swift. There is a function [NSLog][2] in [Foundation][3] which would
be nice to use from C for logging errors etc.

However if you put

```c
#include <Foundation/Foundation.h>
```

in your header file it triggers a multitude of errors due the
Objective C function definitions. The definition of NSLog in the docs
is

```c
void NSLog(NSString *format, ...);
```

The docs entry for [NSString][4] says:

> NSString is “toll-free bridged” with its Core Foundation
> counterpart, CFStringRef. See “Toll-Free Bridging” for more
> information.

So I tried putting an entry in the C include file:

```c
void NSLog(CFStringRef format, ...);
```

But because the include files are also read by the Swift compiler this
also generated errors due to multiple definitions of the function. So
I tried putting the definition in the C source file. This also
requires an entry in the C include file for CFStringRef.

```c
#include <CoreFoundation/CoreFoundation.h>
```

This didn't cause any conflicts and worked nicely.

```c
#include "Audio.h"
void NSLog(CFStringRef format, ...);
// ...
    // Open it
    OSStatus status = AudioComponentInstanceNew(cp, &audio.output);
    if (status != noErr)
    {
        // AudioComponentInstanceNew
        NSLog(CFSTR("Error in AudioComponentInstanceNew %s (%d)"),
              AudioUnitErrString(status), status);
        return status;
    }
```

Having got that to work, I updated the function I originally wrote in
Swift to return a CFStringRef and put it's definition in the include
file.

```c
CFStringRef AudioUnitErrString(OSStatus status);
```

It can then be called from Swift:

```swift
    func DisplayAlert(_ status: OSStatus, _ message: String,
                      _ informativeText: String)
    {
        let alert = NSAlert()
        alert.alertStyle = .warning
        alert.messageText = message

        let error = (status > 0) ? UTCreateStringForOSType(OSType(status))
          .takeRetainedValue() as String :
          AudioUnitErrString(status).takeRetainedValue() as String

        alert.informativeText = informativeText + ": " + error +
          " (" + String(status) + ")"

        alert.runModal()
    }
```

Which also worked.

 [0]: https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language
 [1]: https://developer.apple.com/documentation/coreaudio
 [2]: https://developer.apple.com/documentation/foundation/1395275-nslog
 [3]: https://developer.apple.com/documentation/foundation
 [4]: https://developer.apple.com/documentation/foundation/nsstring

