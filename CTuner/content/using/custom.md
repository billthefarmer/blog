---
title: Custom Temperaments
date: 2019-02-04T15:18:50Z
anchor: custom
weight: 60
---

Some versions have the option of custom themes, loaded from a file or
the app defaults.

## macOS

Use the defaults utility to write custom temperaments to the app's defaults.

```
Cynthia:~ bill$ defaults write org.billthefarmer.tuner Just '(264.00, 275.00, 297.00, 316.80, 330.00, 352.00, 371.25, 396.00, 412.50, 440.00, 475.20, 495.00)'
Cynthia:CTuner bill$ defaults write org.billthefarmer.tuner Temperaments '(Just)'
Cynthia:CTuner bill$ defaults read -app Tuner
{
    Colours = 1;
    Down = 0;
    Filter = 0;
    Just =     (
        "264.00",
        "275.00",
        "297.00",
        "316.80",
        "330.00",
        "352.00",
        "371.25",
        "396.00",
        "412.50",
        "440.00",
        "475.20",
        "495.00"
    );
    Key = 0;
    Ref = 440;
    Strobe = 0;
    Temper = 8;
    Temperaments =     (
        Just
    );
    Trans = 6;
    Zoom = 1;
}
```

If the app is installed in `Applications` you can use `-app Tuner`, if
not use `org.billthefarmer.Tuner`. The commands are.

```
    defaults write -app Tuner <name> '(<value>, <value>, ...)'
    defaults write -app Tuner Temperaments '(<name>)'
```

There should be 12 values separated by commas enclosed in brackets
quoted by single quotes. If the temperament name contains spaces, put
double quotes round it. There should also be an entry listing the
temperaments in the order you would like them to be added to the list.