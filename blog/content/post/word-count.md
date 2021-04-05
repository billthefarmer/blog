---
title: Android Word Count
author: Bill Farmer
type: post
date: 2021-04-05T10:28:06+01:00
url: /word-count/
categories:
    - Hacking
keywords:
    - android
    - word
    - line
    - character
    - count
    - java
---

I was asked to add a word and character count to my [Editor][1]
app. So I looked it up and found a [Gist][2] that uses
[BreakIterator][3]. This relies on an embedded function that
determines what is considered a word.

So I tested it and found that it gave different results to the linux
utility [wc][4]. However, the app already makes great use of regular
expressions, which include an expression for a word character `\w`.

So I wrote a very simple implementation using the regular expression
`\w+`, which means one or more word characters.

```java
    public final static Pattern WORD_PATTERN = Pattern.compile
        ("\\w+", Pattern.MULTILINE);

    // wordcountText
    private void wordcountText()
    {
        int words = 0;
        Matcher matcher = WORD_PATTERN.matcher(textView.getText());
        while (matcher.find())
        {
            words++;
        }

        if (customView != null)
        {
            String string = String.format(Locale.getDefault(), "%d\n%d",
                                          words, textView.length());
            customView.setText(string);
        }
    }
```

This gives identical results to the utility wc for text files, but
differs for source code files, which is probably due to differing
definitions of what constitutes a word. It would be possible to add
the line count as well as the character count, but there isn't room in
the toolbar of an android app for three lines of text.

To trigger a word count when the text changes in the editor I added
some code to the `onCreate()` function and the text changed listener.

```java
        updateWordCount = () -> wordcountText();

                    if (updateWordCount != null)
                    {
                        textView.removeCallbacks(updateWordCount);
                        textView.postDelayed(updateWordCount, UPDATE_DELAY);
                    }
```

This updates the word count in the background after a small delay from
the last text change, so it is not running until necessary.

 [1]: https://billthefarmer.github.io/editor/
 [2]: https://gist.github.com/ElegyD/65ad990d505ee20239ef5a3c16eec951
 [3]: https://developer.android.com/reference/java/text/BreakIterator
 [4]: https://linux.die.net/man/1/wc
 
