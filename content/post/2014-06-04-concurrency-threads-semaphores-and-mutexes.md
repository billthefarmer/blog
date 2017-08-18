---
title: Concurrency, Threads, Semaphores and Mutexes
author: Bill Farmer
type: post
date: 2014-06-04T15:45:20+00:00
url: /concurrency-threads-semaphores-and-mutexes/
categories:
  - Hacking

---
I have written several applications in both Java and C that involve running an audio thread in the background to read or write audio from or to the audio system while displaying the results and interacting with the user in the foreground. I have used lots of threads, but never a semaphore or mutex apart from the odd boolean variable.

This issue is reminiscent of back in the day when you actually had or could get a circuit diagram of your computer and could understand it. One of the first machines I had suffered from display corruption when the display was updated due to contention between the processor and the display system. It was fairly simply fixable by using a spare inverter to reverse the phase of the display system clock to remove the contention.

The applications I have written involve a loop running with about a 100ms repeat time. Every time around the loop various flags or values will be checked and used by the algorithms to produce the results. These flags and values can only be changed by user interaction with the application, so the chance of contention is vanishingly small. However, with 32 bit and 64 bit processors, fetching or writing a word is an atomic operation, so even if there were contention, you can&#8217;t split or interrupt an atomic operation. So you must get the value just before or after it was changed.

What is interesting is what you are supposed to do with the case where you&#8217;ve got an event driven audio library function writing data into a buffer, while concurrently an asynchronous  display callback function is reading and displaying it. I haven&#8217;t worried about it, it just works.

I have had to eat my words to a limited extent. I have had an issue reported with my [Android Midi Driver][1]. A user has reported lockups with lots of midi events, so suggested using a synchronised queue, which I have done. I consider that a special case, however, as there is both Java and native code running concurrently using the same native library.

 [1]: https://github.com/billthefarmer/mididriver
