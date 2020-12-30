---
title: Lambda Expressions
author: Bill Farmer
type: post
date: 2020-03-26T13:02:53Z
url: /lambda-expressions/
categories:
    - Hacking
keywords:
    - lambda
    - expression
    - anonymous
    - function
    - javascript
    - java
    - objective
    - c
---

Lambda expressions or anonymous functions can be quite useful. For
example using [jQuery][1] in JavaScript all the work gets done in
anonymous functions unless you particularly want to extract them and
give them names...

```javascript
jQuery(document).ready(function($) {
    // ...
    // Process the start button
    $("#start").click(function() {
        question = 0;
        $("#data").css("display", "none");
        $(".intro").fadeOut(function() {
        // Do stuff
        });
    // ...
    });
});
```

In C++ the syntax is curious in that there are a pair of square
brackets enclosing 'captured' variables...

```C
    // Must have at least one lambda expression
    gdk_threads_add_idle([](gpointer widget) -> gboolean
    {
        gtk_widget_queue_draw(GTK_WIDGET(widget));
        return G_SOURCE_REMOVE;
    }, widget);
```

In Objective C the syntax is even more peculiar. The `ProcessAudio`
function is called a block. I tried to make it a lambda expression but
had to give up. Whether this is an Objective C or C construct I don't
know...

```C
// Input proc
OSStatus InputProc(void *inRefCon, AudioUnitRenderActionFlags *ioActionFlags,
		   const AudioTimeStamp *inTimeStamp, UInt32 inBusNumber,
		   UInt32 inNumberFrames, AudioBufferList *ioData)
{
    static AudioBufferList abl =
	{1, {1, 0, nil}};
    // ...
    // Run in main queue
    dispatch_async(dispatch_get_main_queue(), ProcessAudio);

    return noErr;
}

// Process audio
void (^ProcessAudio)() = ^
{
    enum
    {kTimerCount = 16};
    // ...
};
```

In java they can be used for callbacks or listeners. The syntax is
simpler, you don't even need the brackets if there's only one
argument...

```java
                showEdit = () ->
                {
                    startAnimation(edit, R.anim.fade_in, View.VISIBLE);
                    scrollUp = false;
                    scrollDn = false;
                };
                // ...
                        // Show button delayed
                        markdownView.removeCallbacks(showEdit);
                        markdownView.postDelayed(showEdit, VISIBLE_DELAY);

            // On click
            accept.setOnClickListener(v ->
            {
                // Get text
                if (display)
                    loadMarkdown();
                // ...
            });
```

 [1]: https://jquery.com
