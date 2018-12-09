---
title: Expand Android Selection
author: Bill Farmer
type: post
date: 2018-12-09T12:21:39Z
categories:
    - Hacking
keywords:
    - android
    - selection
    - context menu
    - action mode
---

When editing data or source files it would be useful to have
selections triggered by a double tap or long press expand to enclosing
delimiters or brackets.

There is a section in the [Android docs][1] which explains how to set
up your own context menu or contextual action mode, but not how to
change the selection.

This method is fine if you have only one text editor view in your app,
but would be difficult with more than one as there is no way to
identify the originating view. In Java a regular expression (regex)
can only search forwards, but not backwards, so we search forwards
using a regex, work out what to look for backwards, and use a standard
string reverse search to find the start of the extended selection. The
regex contains bracket characters and quote characters. If the forward
search finds an opening bracket, we don't change the selection. If the
extended selection contains a newline we don't change the selection
because we have probably got a closing quote on one line and an
opening quote on another. If the delimiter is a semicolon we look back
for a newline.

```java

    public final static String PATTERN_CHARS =
        "[\\(\\)\\[\\]\\{\\}\\<\\>\"'`,;]";
    public final static String INVALID_CHARS = "([{<";

    // onActionModeStarted
    @Override
    public void onActionModeStarted(ActionMode mode)
    {
        super.onActionModeStarted(mode);

        // If there's a file
        if (file != null)
        {
            // Get the mime type
            String type = FileUtils.getMimeType(file);
            // If the type is unknown or not text/plain
            if (type == null || !type.equals(TEXT_PLAIN))
            {
                // Get the start and end of the selection
                int start = textView.getSelectionStart();
                int end = textView.getSelectionEnd();
                // And the text
                String text = textView.getText().toString();

                // Get a pattern and a matcher for delimiter
                // characters
                Pattern pattern =
                    Pattern.compile(PATTERN_CHARS, Pattern.MULTILINE);
                Matcher matcher =
                    pattern.matcher(text);

                // Find the first match after the end of the selection
                if (matcher.find(end))
                {
                    // Update the selection end
                    end = matcher.start();

                    // Get the matched char
                    char c = text.charAt(end);

                    // Check for opening brackets
                    if (INVALID_CHARS.indexOf(c) == -1)
                    {
                        switch (c)
                        {
                            // Check for close brackets and look for
                            // the open brackets
                        case ')':
                            c = '(';
                            break;

                        case ']':
                            c = '[';
                            break;

                        case '}':
                            c = '{';
                            break;

                        case '>':
                            c = '<';
                            break;

                            // Check for semicolon and look for eol
                        case ';':
                            c = '\n';
                            break;
                        }

                        // Do reverse search
                        start = text.lastIndexOf(c, start) + 1;

                        // Check for included newline
                        if (start < text.lastIndexOf('\n', end))
                            // Update selection
                            textView.setSelection(start, end);
                    }
                }
            }
        }
    }
```

 [1]: https://developer.android.com/guide/topics/ui/menus#context-menu
