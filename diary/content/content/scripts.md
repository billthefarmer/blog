---
title: Scripts
date: 2020-02-16T19:12:20Z
anchor: scripts
weight: 220
---

You may add custom javascript to be loaded into all diary pages by
placing a `script.js` file in the `Diary/js` folder. Use the built in
editor to create a script file. You must use the editor `accept` tick
button to save the edits. Errors in the script will be logged by the
[WebView](https://developer.android.com/reference/android/webkit/WebView)
which displays the page. See
[Javascript tutorial](https://www.w3schools.com/js).

If you want to use javascript libraries or write a large script it
might be a good idea to use the Google
[Closure Compiler](https://developers.google.com/closure/compiler) to
check and minimise your code. It will handle multiple input files.
