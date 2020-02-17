---
title: Maps
date: 2020-02-16T19:12:00Z
anchor: maps
weight: 180
---

You may put [OpenStreetMap](http://www.openstreetmap.org) maps in
diary pages with the syntax `[<lat>,<lng>]`, or
`[osm:<lat>,<lng>]`. Because of differing conventions in different
locales, the app parser will accept a comma or a dot (`[,.]`) for the
decimal point, and a comma or a semicolon (`[,;]`) for the co-ordinate
separator. Use the correct convention for your locale. Geo uris
received from other apps will be converted to `[osm](<lat>,<lng>)`
syntax in the diary entry. Diary entries using `[<lat>,<lng>]` syntax
will be converted to geo uri syntax. **Caution** &ndash; geo uris use a
period (`[.]`) for a decimal point and a comma (`[,]`) for the
co-ordinate separator regardless of locale.
