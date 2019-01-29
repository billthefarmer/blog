---
title: Musical Temperaments
author: Bill Farmer
type: post
date: 2019-01-29T20:58:13Z
categories:
    - Hacking
keywords:
    - android
    - musical
    - temperament
    - tuner
---

My tuner app uses equal temperament because that is the current
standard and it is relatively easy to calculate using double precision
arithmetic and maths library functions. First, declare some constants.

```java
    private static final int OCTAVE = 12;
    private static final int EQUAL = 8;
    private static final int A_OFFSET = 9;
    private static final int C5_OFFSET = 57;
```
<br />
The calculation first produces the cents relative to the reference,
usually 440Hz. Then the note number with an added constant because
octaves start at **C**, not **A**. Then the reference frequency for
that note, then the cents difference.

```java
    // Cents relative to reference
    double cf = -12.0 * log2(reference / xf[i]);

    // Note number
    int note = Math.round(cf) + C5_OFFSET;

    // Reference note frequency
    double nearest = reference * Math.pow(2.0, Math.round(cf) / 12.0);

    // Cents relative to reference note
    double cents = -12.0 * log2(nearest / frequency) * 100.0;
```
<br />
I have a large lookup table for 32 temperaments, part of which is shown here.

```java
        // Bach (Klais)
        {262.76, 276.87, 294.30, 311.46, 328.70, 350.37,
         369.18, 393.70, 415.30, 440.00, 467.18, 492.26},

        // Just (Barbour)
        {264.00, 275.00, 297.00, 316.80, 330.00, 352.00,
         371.25, 396.00, 412.50, 440.00, 475.20, 495.00},

        // Equal
        {1.000000000, 1.059463094, 1.122462048, 1.189207115,
         1.259921050, 1.334839854, 1.414213562, 1.498307077,
         1.587401052, 1.681792831, 1.781797436, 1.887748625},

        // Pythagorean
        {1.000000000, 1.067871094, 1.125000000, 1.185185185,
         1.265625000, 1.333333333, 1.423828125, 1.500000000,
         1.601806641, 1.687500000, 1.777777778, 1.898437500},
```
<br />
Some of the entries use actual frequencies, others use ratios. To
calculate the adjustment for a given temperament it is necessary to
calculate the ratio between the current note and **A** for that key,
both for equal temperament and for that temperament.

```java
    // Octave note number
    int n = (note - key + OCTAVE) % OCTAVE;
    // A note number
    int a = (A_OFFSET - key + OCTAVE) % OCTAVE;

    // Temperament ratio
    double temperamentRatio = temperaments[temperament][n] / temperaments[temperament][a];
    // Equal ratio
    double equalRatio = temperaments[EQUAL][n] / temperaments[EQUAL][a];

    // Temperament adjustment
    double temperamentAdjust = temperamentRatio / equalRatio;
```
</br />
Then adjust the reference note frequency for that temperament and key.

```java
    // Reference note frequency
    double nearest = reference * Math.pow(2.0, Math.round(cf) / 12.0) * temperamentAdjust;
```
<br />
Adjusting the nearest reference frequency adjusts everything
calculated following, the cents difference etc. In equal temperament
the ratios cancel each other out, making no difference. Adding more
temperaments is then just a matter of adding entries to the table.
