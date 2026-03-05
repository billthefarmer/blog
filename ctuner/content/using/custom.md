---
title: Custom Temperaments
date: 2019-02-04T15:18:50Z
anchor: custom
weight: 70
---

Some versions have the option of custom temperaments, loaded from a
file or the app defaults.

### Android

You may load custom temperaments from the **Settings** menu. The file
should contain one or more entries formatted as property entries.  The
format is documented in Android Java [Properties][1].

```properties
# Custom Temperaments
Custom: 1.000000000, 1.067871094, 1.125000000, 1.185185185, \
        1.265625000, 1.333333333, 1.423828125, 1.500000000, \
        1.601806641, 1.687500000, 1.777777778, 1.898437500

# 41edo 12 note subset
41edo: 0.995933333333333, 1.07023430212233, 1.122462048, 1.17711684266417, \
       1.26504472893667, 1.3266973308906, 1.42571583230427, 1.49526051927677, \
       1.5680347591656, 1.68521247642303, 1.76730548352053, 1.8992638916125
```

Entries will be loaded and displayed in Settings in alphabetical
order at the end of the list. Erroneous entries will either be ignored
or replaced with ones (1.0).

 [1]: https://developer.android.com/reference/java/util/Properties#load(java.io.Reader)
