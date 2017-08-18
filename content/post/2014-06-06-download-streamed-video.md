---
title: Download streamed video
author: Bill Farmer
type: post
date: 2014-06-06T16:03:16+00:00
url: /download-streamed-video/
categories:
  - Hacking

---
Online video providers tend to stream the video to a javascript player, so making it extremely difficult to capture the video. <a title="http://words.bombast.net" href="http://words.bombast.net/?p=75" target="_blank">This blog</a> explains a method using Chrome to spoof the user agent string, pretending to be an iPad. Unfortunately, this doesn&#8217;t seem to work any more in the latest version of Chrome.

So I tried installing a user agent spoofing plugin in Firefox. It seemed to work, but the video didn&#8217;t play, so I couldn&#8217;t find the url. I next tried Firefox on my Android tablet and the video played. To find the url of the video I used Firefox remote debugging. <a title="https://developer.mozilla.org/en-US/docs/Tools/Remote_Debugging/Firefox_for_Android" href="https://developer.mozilla.org/en-US/docs/Tools/Remote_Debugging/Firefox_for_Android" target="_blank">This page</a> describes how to set it up in detail.

In the web console select the Console tab and turn everything off except Net. Refresh the page on the Android device and find the url of the video in the list of urls displayed. Copy the url and download the video.