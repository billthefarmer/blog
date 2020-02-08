---
title: Derive Edit Position from Markdown
author: Bill Farmer
type: post
date: 2020-02-08T18:24:23Z
url: /derive-edit-position/
categories:
  - Hacking
keywords:
    - android
    - markdown
    - edit
    - position
---

I had a request to switch to the edit view from the markdown view at
the double tap position in my [Diary][1] app. The app already has a
gesture detector so I just had to add an [onDoubleTap][2] function.

To get the tap position in the markdown view it is necessary to
compensate for the position of the view on the screen, scrolling, and
the display density.

```java
        // onDoubleTap
        @Override
        public boolean onDoubleTap(MotionEvent e)
        {
            if (shown)
            {
                int[] l = new int[2];
                markdownView.getLocationOnScreen(l);

                // Get tap position
                float y = e.getY() - l[1];

                int scrollY = markdownView.getScrollY();
                int contentHeight = markdownView.getContentHeight();
                float density = getResources().getDisplayMetrics().density;

                // Get markdown position
                final float p = (y + scrollY) / (contentHeight * density);
```

The function to get the view location on screen is a bit arcane in
that you must provide a two element array for the X and Y
co-ordinates. The position as a proportion of the total length of the
displayed markdown is calculated from the corrected Y position, the
scroll position and the density.

Switch to the edit view and close any open search.

```java
                // Animation
                animateEdit();

                // Close text search
                if (searchItem.isActionViewExpanded())
                    searchItem.collapseActionView();
```

It is necessary to scroll after a delay, otherwise it gets ignored. To
find the position in the edit view it is necessary to get the total
height of the text, calculate the Y position as a proportion, and then
use a series of functions to get the text line, the offset in the text
to set the selection, and the position of the line to scroll to it.

```java

                // Scroll after delay
                textView.postDelayed(() ->
                {
                    int h = textView.getLayout().getHeight();
                    int v = Math.round(h * p);

                    // Get line
                    int line = textView.getLayout().getLineForVertical(v);
                    int offset = textView.getLayout()
                        .getOffsetForHorizontal(line, 0);
                    textView.setSelection(offset);

                    // get text position
                    int position = textView.getLayout().getLineBaseline(line);

                    // Scroll to it
                    int height = scrollView.getHeight();
                    scrollView.smoothScrollTo(0, position - height / 2);
                }, POSITION_DELAY);

                shown = false;
                return true;
            }
            return false;
        }
```

This method is not completely accurate because of embedded images,
text styles, etc, but good enough to get the cursor and scroll
position in about the right place.

 [1]: https://github.com/billthefarmer/diary
 [2]: https://developer.android.com/reference/android/view/GestureDetector.SimpleOnGestureListener.html#onDoubleTap(android.view.MotionEvent)