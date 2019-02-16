---
title: Custom Temperaments
date: 2019-02-04T15:18:50Z
anchor: custom
weight: 70
---

Some versions have the option of custom temperaments, loaded from a
file or the app defaults.

### Android

Use a text editor to create a file in `Tuner/Custom.txt` in the user area.
The file should contain one or more entries formatted as property entries.
The format is documented in Android Java [Properties][1].

```shell
# Custom Temperaments
Custom: 1.000000000, 1.067871094, 1.125000000, 1.185185185, \
        1.265625000, 1.333333333, 1.423828125, 1.500000000, \
        1.601806641, 1.687500000, 1.777777778, 1.898437500
```

Entries will be loaded and displayed in Settings in alphabetical
order at the end of the list. Erroneous entries will either be ignored
or replaced with ones (1.0).

 [1]: https://developer.android.com/reference/java/util/Properties#load(java.io.Reader)
