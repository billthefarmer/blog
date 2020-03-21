---
title: "Oscilloscope"
date: 2019-05-22T10:52:04+01:00
anchor: scope
weight: 16
---

|     |     |
| --- | --- |
| ![Scope][1] | ![Scope-swift][2] |
| ![Scope-android][3] |   |


  * Timebase 0.1ms &ndash; 0.5sec
  * Resolution 0.022ms @ 44100 samples/sec
  * Single shot
  * Adjustable sync level
  * Storage
  * Index

The icons on the toolbar are, from left to right:
 
 *  **Bright line** &ndash; Defeats the sync and single shot
 *  **Single shot** &ndash; Click the trigger icon to get a trace
 *  **Trigger** &ndash; Click for a trace
 *  **Timebase** &ndash; Drops a submenu of timebase timings
 *  **Storage** &ndash; Does not clear the display between traces
 *  **Clear** &ndash; Clears the display
 *  **Left** &ndash; Moves the trace to the left
 *  **Right** &ndash; Moves the trace to the right
 *  **Start** &ndash; Moves the trace to the start
 *  **End** &ndash; Moves the trace to the end
 *  **Reset** &ndash; Resets to default settings

### Using
Use the toolbar buttons to access functionality. Clicking on the left
Y scale turns on the sync level control.  Clicking on the scope
display turns on the cursor line, which may be shifted left and right
using the left and right arrow keys. The cursor line shows the time
from the start of the trace in current timebase units, and the nominal
trace value at that point. There is no calibration because of the
various level controls in the audio system. The move to start and
reset toolbar buttons clear the sync level and the cursor.

### Downloads
Windows and Mac versions [here][4], Android version [here][5].

 [1]: images/Scope.png
 [2]: images/Scope-swift.png
 [3]: images/Scope-android.png
 [4]: https://github.com/billthefarmer/audiotools/releases (https://github.com/billthefarmer/audiotools/releases)
 [5]: https://github.com/billthefarmer/scope/releases (https://github.com/billthefarmer/scope/releases)
