---
title: Draw Musical Staff
author: Bill Farmer
type: post
date: 2019-01-13T18:35:26Z
categories:
    - Hacking
keywords:
    - android
    - draw
    - musical
    - staff
    - clef
---

A musical staff is not a trivial thing to draw because of the complex
curves needed for the treble and base clef. What is needed is a source
of vector drawing coordinates.

The data used was originally intended for use in postscript, so the
vertical axis is reversed.

```java
    // Treble clef
    private static final float tc[][]=
    {
        {-6, 16},  {-8, 13},  {-14, 19},  {-10, 35},  {2, 35},  {8, 37},
        {21, 30},  {21, 17},  {21, 5},  {10, -1},  {0, -1},  {-12, -1},
        {-23, 5},  {-23, 22},  {-23, 29},  {-22, 37},  {-7, 49},  {10, 61},
        {10, 68},  {10, 73},  {10, 78},  {9, 82},  {7, 82},  {2, 78},
        {-2, 68},  {-2, 62},  {-2, 25},  {10, 18},  {11, -8},  {11, -18},
        {5, -23},  {-4, -23},  {-10, -23},  {-15, -18},  {-15, -13},
        {-15, -8},  {-12, -4},  {-7, -4},  {3, -4},  {3, -20},  {-6, -17},
        {-5, -23},  {9, -20},  {9, -9},  {7, 24},  {-5, 30},  {-5, 67},
        {-5, 78},  {-2, 87},  {7, 91},  {13, 87},  {18, 80},  {17, 73},
        {17, 62},  {10, 54},  {1, 45},  {-5, 38},  {-15, 33},  {-15, 19},
        {-15, 7},  {-8, 1},  {0, 1},  {8, 1},  {15, 6},  {15, 14},  {15, 23},
        {7, 26},  {2, 26},  {-5, 26},  {-9, 21},  {-6, 16}
    };

    // Bass clef
    private static final float bc[][] =
    {
        {-2.3f,3},
        {6,7}, {10.5f,12}, {10.5f,16},
        {10.5f,20.5f}, {8.5f,23.5f}, {6.2f,23.3f},
        {5.2f,23.5f}, {2,23.5f}, {0.5f,19.5f},
        {2,20}, {4,19.5f}, {4,18},
        {4,17}, {3.5f,16}, {2,16},
        {1,16}, {0,16.9f}, {0,18.5f},
        {0,21}, {2.1f,24}, {6,24},
        {10,24}, {13.5f,21.5f}, {13.5f,16.5f},
        {13.5f,11}, {7,5.5f}, {-2.0f,2.8f},
        {14.9f,21},
        {14.9f,22.5f}, {16.9f,22.5f}, {16.9f,21},
        {16.9f,19.5f}, {14.9f,19.5f}, {14.9f,21},
        {14.9f,15},
        {14.9f,16.5f}, {16.9f,16.5f}, {16.9f,15},
        {16.9f,13.5f}, {14.9f,13.5f}, {14.9f,15}
    };
```

Both these sets of points are made up of an initial move, followed by
a series of cubic bezier curves. The bass clef has three sections, the
symbol, followed by the two dots.

```java
        // Treble clef
        tclef = new Path();
        tclef.moveTo(tc[0][0], tc[0][1]);
        tclef.lineTo(tc[1][0], tc[1][1]);
        for (int i = 2; i < tc.length; i += 3)
            tclef.cubicTo(tc[i][0], tc[i][1],
                          tc[i + 1][0], tc[i + 1][1],
                          tc[i + 2][0], tc[i + 2][1]);

        // Bass clef
        bclef = new Path();
        bclef.moveTo(bc[0][0], bc[0][1]);
        for (int i = 1; i < 28; i += 3)
            bclef.cubicTo(bc[i][0], bc[i][1],
                          bc[i + 1][0], bc[i + 1][1],
                          bc[i + 2][0], bc[i + 2][1]);

        bclef.moveTo(bc[28][0], bc[28][1]);
        for (int i = 29; i < 35; i += 3)
            bclef.cubicTo(bc[i][0], bc[i][1],
                          bc[i + 1][0], bc[i + 1][1],
                          bc[i + 2][0], bc[i + 2][1]);

        bclef.moveTo(bc[35][0], bc[35][1]);
        for (int i = 36; i < bc.length; i += 3)
            bclef.cubicTo(bc[i][0], bc[i][1],
                          bc[i + 1][0], bc[i + 1][1],
                          bc[i + 2][0], bc[i + 2][1]);
```

It is necessary to scale the paths to fit the view they are to be
drawn in and calculate the line spacing for the staff lines.

```java
        lineHeight = height / 14f;
        margin = width / 32;

        RectF bounds = new RectF();

        // Scale treble clef
        tclef.computeBounds(bounds, false);
        float scale = (height / 2) / (bounds.top - bounds.bottom);
        matrix = new Matrix();
        matrix.setScale(-scale, scale);
        matrix.postTranslate(margin + (lineHeight * 2), - lineHeight);
        tclef.transform(matrix);

        // Scale bass clef
        bclef.computeBounds(bounds, false);
        scale = (lineHeight * 4) / (bounds.top - bounds.bottom);
        matrix.reset();
        matrix.setScale(-scale, scale);
        matrix.postTranslate(margin + lineHeight, lineHeight * 5.4f);
        bclef.transform(matrix);
```

The staff consists of two sets of five lines, closer together than is
usual to allow for a continuous scale from top to bottom. There are
also three leger lines to allow for showing notes off the staff lines.
  
```java

        // Draw staff
        canvas.translate(0, height / 2f);
        for (int i = 1; i < 6; i++)
        {
            canvas.drawLine(margin, i * lineHeight,
                            width - margin, i * lineHeight, paint);
            canvas.drawLine(margin, i * -lineHeight,
                            width - margin, i * -lineHeight, paint);
        }

        // Draw leger lines
        canvas.drawLine((width / 2) - (lineHeight * 1.5f), 0,
                        (width / 2) + (lineHeight * 1.5f), 0, paint);

        canvas.drawLine((width / 2) + (lineHeight * 16.5f),
                        -lineHeight * 6,
                        (width / 2) + (lineHeight * 19.5f),
                        -lineHeight * 6, paint);

        canvas.drawLine((width / 2) - (lineHeight * 16.5f),
                        lineHeight * 6,
                        (width / 2) - (lineHeight * 19.5f),
                        lineHeight * 6, paint);

        // Draw treble and bass clef
        canvas.drawPath(tclef, paint);
        canvas.drawPath(bclef, paint);
```

The bass and treble clef have been made slightly larger than usual as
the view can be quite small on a phone.

![Staff][1]

 [1]: images/2019/01/staff.png
