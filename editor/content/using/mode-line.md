---
title: Mode Line
date: 2020-03-04T17:41:40Z
anchor: mode-line
weight: 280
---

If a line of text is found within the first or last two or three lines
of the file which matches the mode line pattern, the mode of the
editor will be changed after the file is loaded.  The pattern is one
or more text or punctuation characters, white space, the text `ed:`
followed by one or more option patterns separated by white space.

    # ed: [[no]vw] [[no]ww] [[no]sg] [[no]hs] [cs:u] [th:l|d|s|w|b|r] [ts:l|m|s] [tf:m|p|s]

The initial text or punctuation characters are intended to be used to
hide the mode line from compilers and interpreters by commenting it
out.

The option patterns are:

 * **[no]vw** &ndash; View files read only
 * **[no]ww** &ndash; Word wrap
 * **[no]sg** &ndash; Suggestions
 * **[no]hs** &ndash; Syntax highlighting
 * **cs:u** &ndash; Character set &ndash; UTF-8
 * **th:l|d|s|w|b|r** &ndash; Theme &ndash; light, dark, system,
   white, black or retro
 * **ts:l|m|s** &ndash; Text size &ndash; large, medium or small
 * **tf:m|p|s** &ndash; Typeface &ndash; monospace, proportional
   (sans) or serif

The mode line is read after the file is loaded and will change
immediately, except in Android 6, Marshmallow due to an obscure
bug. Rotate the device to change mode.
