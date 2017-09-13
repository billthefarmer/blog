---
title: Fast Fourier Transform
author: Bill Farmer
type: post
date: 2015-03-03T12:44:13+00:00
url: /fast-fourier-transform/
categories:
  - Hacking
keywords:
  - fft
  - java
  - android
  - ffts
---
I wrote a java tuner app a few years ago, so I needed a [Fast Fourier Transform][1] to do the frequency detection. I found a fairly simple implementation in java on the web somewhere, can&#8217;t remember where, tried it, and it worked fine. The frequency detection algorithm comes from [The Dsp Dimension][2].

A few years later I ported it to windows, added a few gizmos for tuning accordions and put it on [Google Code][3]. I was concerned that the FFT implementation was simplistic and did more research online to find better implementations. However, when I actually checked the performance with a profiling tool, I found that the spectrum display code was the bottleneck and the FFT was not significant. I tried a few different FFT implementations anyway, but they didn&#8217;t make a significant difference. I then ported the app to OSX, which has a built in high performance [DSP][4] library, so no problem there.

Later still, I ported it to Android, on [GitHub][5], also on [FDroid][6], and the FFT still worked fine in Java on Android. Then I had a request to add a spectrum display to my [Oscilloscope][7] app, also on [FDroid][8]. When I tried this on my Moto G phone, the spectrum obviously had a problem. When I checked, the problem was the simplistic FFT I have been using. So I did another [web search][9] and discovered [FFTS][10], the allegedly Fastest Fourier Transform in the South. I couldn&#8217;t get this to build for Android using the script provided, so I did a fork on [Github][11] which I rearranged so it will build with the Android build tools. I also built a test app which does the tests shipped with the library. The FFTS library uses dynamic code which modifies itself, and also makes use of the [ARM NEON][12] engine if it is present. All three Android devices we have have NEON support in the processor.

I haven&#8217;t got to testing it on the spectrum app yet.

 [1]: https://en.wikipedia.org/wiki/Fast_Fourier_transform
 [2]: http://www.dspdimension.com/admin/pitch-shifting-using-the-ft
 [3]: https://code.google.com/p/ctuner
 [4]: https://developer.apple.com/library/ios/documentation/Performance/Conceptual/vDSP_Programming_Guide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40005147
 [5]: https://github.com/billthefarmer/tuner/wiki
 [6]: https://f-droid.org/packages/org.billthefarmer.tuner
 [7]: https://github.com/billthefarmer/scope/wiki
 [8]: https://f-droid.org/packages/org.billthefarmer.scope
 [9]: https://duckduckgo.com
 [10]: http://anthonix.com/ffts
 [11]: https://github.com/billthefarmer/ffts-android
 [12]: http://www.arm.com/products/processors/technologies/neon.php
