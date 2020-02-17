---
title: Media
date: 2020-02-16T19:12:10Z
anchor: media
weight: 320
---

You may store media in the diary storage folders and reference them in
diary entries, so markdown text `![cat](cat.jpg)` will display
`cat.jpg` stored in the current month folder which is
`Diary/<year>/<month>` on the sdcard. You may either add media from
media providers like file managers or image managers or receive media
sent by other apps. Media added will be added at the current cursor
position. Media sent by other apps will pop up a date picker. The
media will be appended to the selected page. Content URIs
(`content://`) sent by some media providers and apps will be resolved
to file URIs (`file:///`) if possible. Media will be added using
markdown image syntax (`![<name>](<url>)`), which will be parsed into
an HTML5 media player, text clips will be added as above, URLs will be
added as a link. Media added from removable SD cards not part of the
device storage may work but may not be persistent and is not supported.
