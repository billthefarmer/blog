---
title: Source Code Syntax Highlighting
author: Bill Farmer
type: post
date: 2019-03-24T11:01:35Z
categories:
    - Hacking
keywords:
    - android
    - source
    - code
    - highlight
---

One of the enhancements suggested for my android [editor][1] is syntax
highlighting. The libraries I have found, including the [one][2] used
in this blog, parse the input and produce output in HTML, using styled
spans. [Emacs][3] makes extensive use of [regular expressions][4] for
highlighting.

HTML output is not very useful for a text editor. It could be used
with [Html.fromHtml()][5] to produce styled text. This would then be
loaded back into the editor all while the user is attempting to edit
the text. I don't think so. So I explored alternatives.

C, C++, Objective C, Go, Java, Javascript, Python, Swift and other
C-like languages share a set of mostly common keywords and type
identifiers. There is a convention that classes are capitalised. There
is a convention that constants and macros are all caps. Making use of
these conventions it is possible to highlight source code without
parsing it. It won't get it right all the time, but most of the
time. For example MacOS C and Objective C uses a convention of
`kWhatever` for constants.

Putting all this together, first produce a list of all the keywords
and types in the above languages, sort them and delete duplicates and
make regular expressions. The `\\b` means a word boundary to stop
embedded words being highlighted.

```java
    public final static String KEYWORDS =
        "\\b(abstract|and|arguments|as(m|sert|sociativity)?|auto|break|" +
        "case|catch|chan|char|class|con(st|tinue|venience)|" +
        "de(bugger|f|fault|fer|init|l|lete)|didset|do|dynamic|" +
        "dynamictype|el(if|else)|enum|eval|ex(cept|ec|plicit|port|" +
        "tends|tension|tern)|fal(lthrough|se)|final(ly)?|for|" +
        "friend|from|func(tion)?|get|global|go(to)?|if|" +
        "im(plements|port)|in(fix|it|line|out|stanceof|terface|" +
        "ternal)?|is|lambda|lazy|left|let|map|mut(able|ating)|" +
        "namespace|native|new|nil|none|nonmutating|not|null|" +
        "operator|optional|or|override|package|pass|postfix|" +
        "pre(cedence|fix)|print|private|prot(ected|ocol)|public|" +
        "raise|range|register|required|return|right|select|self|" +
        "set|signed|sizeof|static|strictfp|struct|subscript|super|" +
        "switch|synchronized|template|this|throw|throws|transient|" +
        "true|try|type(alias|def|id|name|of)?|un(ion|owned|signed)|" +
        "using|var|virtual|void|volatile|weak|wh(ere|ile)|willset|" +
        "with|yield)\\b";

    public final static String TYPES =
        "\\b(j?bool(ean)?|(u|j)?(byte|char|double|float|int(eger)?|" +
        "long|short))\\b";
```

Some Javascript libraries can guess the language from the input. Emacs
relies on the file extension, the [Chroma][2] library used here relies
on being told the language. The text editor has the file extension, so
another regular expression to check file extensions.

```java
    public final static String CC_EXT =
        "\\.(c(c|pp|xx|\\+\\+)?|go|h|java|js|m|py|swift)";
```

Check highlighting. The app also does HTML and CSS and has a syntax
highlight option. The highlighting code is called with a small delay
after each action that might change or scroll the text, and after
removing the previous call.

```java
    // checkHighlight
    private void checkHighlight()
    {
        // No syntax
        syntax = NO_SYNTAX;

        // Check extension
        if (highlight && file != null)
        {
            String ext = FileUtils.getExtension(file.getName());
            if (ext != null)
            {
                if (ext.matches(CC_EXT))
                    syntax = CC_SYNTAX;

                else if (ext.matches(HTML_EXT))
                    syntax = HTML_SYNTAX;

                else if (ext.matches(CSS_EXT))
                    syntax = CSS_SYNTAX;

                else
                    syntax = NO_SYNTAX;

                if (textView != null && syntax != NO_SYNTAX)
                {
                    // Set callback
                    if (updateHighlight == null)
                        updateHighlight = () ->
                            highlightText();

                    textView.removeCallbacks(updateHighlight);
                    textView.postDelayed(updateHighlight, UPDATE_DELAY);

                    return;
                }
            }
        }

        // Remove highlighting
        if (updateHighlight != null)
        {
            textView.removeCallbacks(updateHighlight);
            textView.postDelayed(updateHighlight, UPDATE_DELAY);

            updateHighlight = null;
        }
    }
```

Highlight the text. Only highlight the visible region as highlighting
a long source file is too slow. Adding highlighted spans causes the
view hierarchy to re-layout the views which causes the text to scroll
to bring the current selection into view. This is annoying when
scrolling the text, so move the selection if not editing.

```java
    // highlightText
    private void highlightText()
    {
        // Get visible extent
        int top = scrollView.getScrollY();
        int height = scrollView.getHeight();

        int line = textView.getLayout().getLineForVertical(top);
        int start = textView.getLayout().getLineStart(line);

        line = textView.getLayout().getLineForVertical(top + height);
        int end = textView.getLayout().getLineEnd(line);

        // Move selection if outside range
        if (textView.getSelectionStart() < start)
            textView.setSelection(start, start);

        if (textView.getSelectionStart() > end)
        {
            int last = textView.getLayout().getLineStart(line - 1);
            textView.setSelection(last, last);
        }

        // Get editable
        Editable editable = textView.getEditableText();

        // Get current spans
        ForegroundColorSpan spans[] =
            editable.getSpans(0, editable.length(), ForegroundColorSpan.class);
        // Remove spans
        for (ForegroundColorSpan span: spans)
            editable.removeSpan(span);

        Pattern pattern;
        Matcher matcher;

        switch (syntax)
        {
        case CC_SYNTAX:
            pattern = Pattern.compile(KEYWORDS, Pattern.MULTILINE);
            matcher = pattern.matcher(editable);
            matcher.region(start, end);

            while (matcher.find())
            {
                ForegroundColorSpan span = new
                    ForegroundColorSpan(Color.CYAN);

                // Highlight it
                editable.setSpan(span, matcher.start(), matcher.end(),
                                 Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
            }

            pattern = Pattern.compile(TYPES, Pattern.MULTILINE);
            matcher.region(start, end).usePattern(pattern);

            while (matcher.find())
            {
                ForegroundColorSpan span = new
                    ForegroundColorSpan(Color.MAGENTA);

                // Highlight it
                editable.setSpan(span, matcher.start(), matcher.end(),
                                 Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
            }

            pattern = Pattern.compile(CLASS, Pattern.MULTILINE);
            matcher.region(start, end).usePattern(pattern);

            while (matcher.find())
            {
                ForegroundColorSpan span = new
                    ForegroundColorSpan(Color.BLUE);

                // Highlight it
                editable.setSpan(span, matcher.start(), matcher.end(),
                                 Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
            }

            pattern = Pattern.compile(CONSTANT, Pattern.MULTILINE);
            matcher.region(start, end).usePattern(pattern);

            while (matcher.find())
            {
                ForegroundColorSpan span = new
                    ForegroundColorSpan(Color.LTGRAY);

                // Highlight it
                editable.setSpan(span, matcher.start(), matcher.end(),
                                 Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
            }

            pattern = Pattern.compile(CC_COMMENT, Pattern.MULTILINE);
            matcher.region(start, end).usePattern(pattern);

            while (matcher.find())
            {
                ForegroundColorSpan span = new
                    ForegroundColorSpan(Color.RED);

                // Highlight it
                editable.setSpan(span, matcher.start(), matcher.end(),
                                 Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
            }
            break;
        }
    }
```

There are various triggers to cause the highlighting to change -
scrolling the text, changing the text and dismissing the
keyboard. There is no built in check on the keyboard, it can only be
detected by checking view sizes. Only trigger the highlight on
dismissing the keyboard.

```java
            textView.getViewTreeObserver().addOnGlobalLayoutListener(
                new ViewTreeObserver.OnGlobalLayoutListener()
            {
                private boolean keyboard;

                // onGlobalLayout
                @Override
                public void onGlobalLayout()
                {
                    if (updateHighlight != null)
                    {
                        int rootHeight = scrollView.getRootView().getHeight();
                        int height = scrollView.getHeight();

                        boolean shown = (((rootHeight - height) /
                                         (double) rootHeight) >
                                         KEYBOARD_RATIO);

                        if (shown != keyboard)
                        {
                            if (!shown)
                            {
                                textView.removeCallbacks(updateHighlight);
                                textView.postDelayed(updateHighlight,
                                                     UPDATE_DELAY);
                            }

                            keyboard = shown;
                        }
                    }
                }
            });
```

Creating a list of HTML tags and making a regular expression is
similar to creating the C type expression. Creating a list of CSS
styles produces a rather long list, and the `\\b` word boundary
doesn't work properly because of the dashes in the styles. So remove
the dashes and create a list of unique styles which is a much shorter
list.

```java
    public final static String HTML_TAGS =
        "\\b(html|base|head|link|meta|style|title|body|address|article|" +
        "aside|footer|header|h\\d|hgroup|main|nav|section|blockquote|dd|" +
        "dir|div|dl|dt|figcaption|figure|hr|li|main|ol|p|pre|ul|a|abbr|" +
        "b|bdi|bdo|br|cite|code|data|dfn|em|i|kbd|mark|q|rb|rp|rt|rtc|" +
    // ...

    public final static String CSS_STYLES =
        "\\b(action|active|additive|adjust|after|align|all|alternates|" +
        "animation|annotation|area|areas|as|asian|attachment|attr|" +
        "auto|backdrop|backface|background|basis|before|behavior|" +
        "bezier|bidi|blend|block|blur|border|bottom|box|break|" +
    // ...
```

 [1]: https://github.com/billthefarmer/editor (https://github.com/billthefarmer/editor)
 [2]: https://github.com/alecthomas/chroma (https://github.com/alecthomas/chroma)
 [3]: https://www.gnu.org/software/emacs (https://www.gnu.org/software/emacs)
 [4]: https://en.wikipedia.org/wiki/Regular_expression (https://en.wikipedia.org/wiki/Regular_expression)
 [5]: https://developer.android.com/reference/android/text/Html.html#fromHtml(java.lang.String) (https://developer.android.com/reference/android/text/Html.html)
