---
title: Media
date: 2020-02-15T19:39:29Z
anchor: media
weight: 330
---

You may store media in the note storage folder and reference them in
notes, so markdown text `![cat](cat.jpg)` will display `cat.jpg`
stored in the note folder. You may either add media from media
providers like file managers or image managers or receive media sent
by other apps. Media added will be added at the current cursor
position. Media sent by other apps will be added at the current cursor
position, or in a new note if no note is open. Content URIs
(`content://`) sent by some media providers and apps will be resolved
to file URIs (`file:///`) if possible. Media will be added using
markdown image syntax (`![<name>](<url>)`), which will be parsed into
an HTML5 media player, text clips will be added as above, URLs will be
added as a link. Media added from removable SD cards not part of the
device storage may work but may not be persistent and is not supported.
