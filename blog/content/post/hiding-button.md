---
title: Hiding Edit Button
author: Bill Farmer
date: 2020-02-15T20:59:47Z
url: /hiding-button/
categories:
    - Hacking
keywords:
    - android
    - animation
    - hiding
    - button
---

My [Diary][1] and [Notes][2] apps have a floating button in the corner
of the screen which is used to switch between editing markdown text
and viewing the result. Although it is part of the android Material
Design, this button has been the subject of several issues raised by
users.

I have seen apps which hide this button on scrolling, and this was one
of the suggestions by users. Getting this to work correctly turned out
to be more complex than initially considered, requiring two boolean
flags and a lambda expression.

These flags are required to keep track of scrolling direction.

```java
    private final static int VISIBLE_DELAY = 2048;

    private boolean scrollUp = false;
    private boolean scrollDn = false;
```

The scroll change listener is only supported from android version 6
Marshmallow. The lambda expression `showEdit()` is required to restore
the button after a delay. The animation is started and the flags
reset.


```java
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
            {
                final Runnable showEdit = () ->
                {
                    startAnimation(edit, R.anim.fade_in, View.VISIBLE);
                    scrollUp = false;
                    scrollDn = false;
                };
```

On detecting a scroll down the button is hidden using an animation and
the flag is set to ensure it only happens once. The delayed call to
`showEdit()` is removed and posted again until the view stops
scrolling, so the delay is from when it stops.

```java
                // onScrollChange
                markdownView.setOnScrollChangeListener((v, x, y, oldX, oldY) ->
                {
                    // Scroll up
                    if (y > oldY)
                    {
                        if (!scrollUp)
                        {
                            // Hide button
                            startAnimation(edit, R.anim.fade_out, View.INVISIBLE);

                            // Set flags
                            scrollUp = true;
                            scrollDn = false;
                        }

                        // Show button delayed
                        markdownView.removeCallbacks(showEdit);
                        markdownView.postDelayed(showEdit, VISIBLE_DELAY);
                    }
```

On a scroll down the button is restored and the flags are set to
ensure it only happens once. The delayed call is reset to take care of
bounce so the button isn't restored again.

```java
                    else if (!scrollDn)
                    {
                        // Set flags
                        scrollUp = false;
                        scrollDn = true;

                        // Show button
                        if (edit.getVisibility() != View.VISIBLE)
                        {
                            startAnimation(edit, R.anim.fade_in, View.VISIBLE);
                            markdownView.removeCallbacks(showEdit);
                        }
                   }
                });
            }
```

The app includes previously added functions to hide and restore the
button using long touches. The flags are reset to stop unwanted
interaction.

```java
            // On long click
            markdownView.setOnLongClickListener(v ->
            {
                // Show button
                if (edit.getVisibility() != View.VISIBLE)
                    startAnimation(edit, R.anim.fade_in, View.VISIBLE);
                scrollUp = false;
                scrollDn = false;
                return false;
            });
        }
```

There is no point having an animation on hiding the button because it
is hidden by the user's finger. The flag is set so it isn't hidden
again on scroll up.

```java
        if (edit != null)
        {
            // On long click
            edit.setOnLongClickListener(v ->
            {
                // Hide button
                v.setVisibility(View.INVISIBLE);
                scrollUp = true;
                scrollDn = false;
                return true;
            });
        }
```

The function to start the button animation.

```java
    // startAnimation
    private void startAnimation(View view, int anim, int visibility)
    {
        Animation animation = AnimationUtils.loadAnimation(this, anim);
        view.startAnimation(animation);
        view.setVisibility(visibility);
    }
```

These functions are used for both the edit view and the markdown view
in the apps.

 [1]: https://github.com/billthefarmer/diary
 [2]: https://github.com/billthefarmer/notes
