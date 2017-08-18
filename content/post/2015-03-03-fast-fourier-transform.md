---
title: Fast Fourier Transform
author: Bill Farmer
type: post
date: 2015-03-03T12:44:13+00:00
url: /fast-fourier-transform/
categories:
  - Hacking

---
I wrote a java tuner app a few years ago, so I needed a <a href="https://en.wikipedia.org/wiki/Fast_Fourier_transform" target="_blank">Fast Fourier Transform</a> to do the frequency detection. I found a fairly simple implementation in java on the web somewhere, can&#8217;t remember where, tried it, and it worked fine. The frequency detection algorithm comes from <a href="http://www.dspdimension.com/admin/pitch-shifting-using-the-ft" target="_blank">The Dsp Dimension.</a>

A few years later I ported it to windows, added a few gizmos for tuning accordions and put it on <a href="https://code.google.com/p/ctuner" target="_blank">Google Code</a>. I was concerned that the FFT implementation was simplistic and did more research online to find better implementations. However, when I actually checked the performance with a profiling tool, I found that the spectrum display code was the bottleneck and the FFT was not significant. I tried a few different FFT implementations anyway, but they didn&#8217;t make a significant difference. I then ported the app to OSX, which has a built in high performance <a href="https://developer.apple.com/library/ios/documentation/Performance/Conceptual/vDSP_Programming_Guide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40005147" target="_blank">DSP</a> library, so no problem there.

Later still, I ported it to Android, on <a href="https://github.com/billthefarmer/tuner/wiki" target="_blank">GitHub</a>, also on <a href="https://f-droid.org/repository/browse/?fdcategory=Multimedia&fdid=org.billthefarmer.tuner&fdpage=1" target="_blank">FDroid</a>, and the FFT still worked fine in Java on Android. Then I had a request to add a spectrum display to my <a href="https://github.com/billthefarmer/scope/wiki" target="_blank">Oscilloscope </a>app, also on <a href="https://f-droid.org/repository/browse/?fdfilter=oscilloscope&fdcategory=Multimedia&fdid=org.billthefarmer.scope" target="_blank">FDroid</a>. When I tried this on my Moto G phone, the spectrum obviously had a problem. When I checked, the problem was the simplistic FFT I have been using. So I did another <a href="https://duckduckgo.com" target="_blank">web search</a> and discovered <a href="http://anthonix.com/ffts" target="_blank">FFTS</a>, the allegedly Fastest Fourier Transform in the South. I couldn&#8217;t get this to build for Android using the script provided, so I did a fork on <a href="https://github.com/billthefarmer/ffts-android" target="_blank">Github</a> which I rearranged so it will build with the Android build tools. I also built a t<a href="https://github.com/billthefarmer/ffts-test-android" target="_blank">est app</a> which does the tests shipped with the library. The FFTS library uses dynamic code which modifies itself, and also makes use of the <a href="http://www.arm.com/products/processors/technologies/neon.php" target="_blank">ARM NEON</a> engine if it is present. All three Android devices we have have NEON support in the processor.

I haven&#8217;t got to testing it on the spectrum app yet.