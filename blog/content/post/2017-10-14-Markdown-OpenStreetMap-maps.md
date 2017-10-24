---
title: Markdown OpenStreetMap Maps
author: Bill Farmer
type: post
date: 2017-10-14T12:59:06+01:00
url: /markdown-openstreetmap-maps/
categories:
  - Hacking
keywords:
  - markdown
  - openstreetmap
  - maps
  - android
---

There does not seem to be a convention for showing maps or
co-ordinates in Markdown, but almost anything gets put in square
brackets `[<anything>]`.

I had a request from [Marco][1] the other day to put
[OpenStreetMap][2] maps in my [Diary][3] app using

```
  [<lat>,<lng>]
```

syntax. So I did a bit of a search to see if there was a convention in
Markdown for co-ordinates and couldn't find anything. So we seem to
have broken new ground here.

The OpenStreetMap api for accessing their maps is quite
straightforward, you just use
`http://www.openstreetmap.org/#map/<level>/<lat>/<lng>` or various
variations on that. However the syntax for their embedded iframe API
doesn't seem to be documented, but you can get the HTML for an
embedded map from their map page and customise it.

```html
<iframe width="425" height="350"
        src="http://www.openstreetmap.org/export/embed.html?
        bbox=3.1528186798095708%2C42.2577141531011%2C3.1886529922485356%2C42.27241862881183&amp;
        layer=mapnik"
        style="border: 1px solid black">
</iframe>
<br/>
<small>
  <a href="http://www.openstreetmap.org/#map=16/42.2651/3.1707">
    View Larger Map
  </a>
</small>
```

I have removed some non HTML5 code from the above. So the goal is to
parse something that looks like `[42.265,3.17]` and produce something
that looks like the above. The obvious tool in android Java is the
[Pattern][4] and [Matcher][5] classes, which parse regular
expressions. The regular expression for the pattern is

```
    "\\[(?:osm:)?(-?\\d+[,.]\\d+)[,;](-?\\d+[,.]\\d+)\\]"
```

The double backslashes are because it's a string, so one of them will
be removed before the matcher gets to see it. I also have to deal with
European locales which use different conventions for co-ordinates. I
added the option to have a prefix '`osm:`', as using prefixes seems to
be a markdown convention. It also allows for having different
maps. The template for the iframe is

```html
    "<iframe width=\"560\" height=\"420\"
    src=\"http://www.openstreetmap.org/export/embed.html?
    bbox=%f,%f,%f,%f&amp;layer=mapnik\"
    style=\"border: 1px solid black\">
    </iframe><br/>
    <small><a href=\"http://www.openstreetmap.org/#map=16/%f/%f\">
    View Larger Map</a></small>\n"
```

I have replaced the '`%2C`'s with commas, and changed the dimensions
slightly, which seems to work OK. The android Java code to do this is

```java
    // loadMarkdown
    private void loadMarkdown(String text)
    {
        markdownView.loadMarkdown(getBaseUrl(), markdownCheck(text),
                                  getStyles());
    }

    // markdownCheck
    private String markdownCheck(String text)
    {
        // Check for media
        text = mediaCheck(text);

        // Check for map
        return mapCheck(text);
    }

    // mapCheck
    private String mapCheck(String text)
    {
        StringBuffer buffer = new StringBuffer();

        Pattern pattern = Pattern.compile(MAP_PATTERN, Pattern.MULTILINE);
        Matcher matcher = pattern.matcher(text);

        // Find matches
        while (matcher.find())
        {
            double lat = 1.0;
            double lng = 1.0;

            try
            {
                lat = Double.parseDouble(matcher.group(1));
                lng = Double.parseDouble(matcher.group(2));
            }

            // Ignore parse error
            catch (Exception e)
            {
                continue;
            }

            // Create replacement iframe
            String replace =
                String.format(new Locale("en"), MAP_TEMPLATE,
                              lng - 0.005, lat - 0.005,
                              lng + 0.005, lat + 0.005,
                              lat, lng);

            // Substitute replacement
            matcher.appendReplacement(buffer, replace);
        }

        // Append rest of entry
        matcher.appendTail(buffer);

        return buffer.toString();
    }
```

The input string is the markdown code to be parsed, the output is the
same code with matched template substitutions. I have just added and
subtracted 0.005 from the given co-ordinates to create a bounding box
so the given point is in the centre. The API doesn't seem to be too
fussy. It is important to use the `"en"` locale so the output uses
dots for decimal points. The [appendReplacement][6] and
[appendTail][7] Java methods are ready made for this pattern matching
and replacement.

#### Update

After I had implemented this It was suggested that perhaps I should be
using a geo Uri (`![](geo:<lat,<lng>)`) for the map markdown syntax. I
hadn't thought of that, and it raises the possibility of receiving a
geo Uri and turning it into a map. So I decided to convert existing
`[<lat>,<lng>]` markdown to `![osm](geo:<lat>,<lng>)` markdown, and
convert that to an iframe as before. The code to do that is very
similar to the above with updated templates.

 [1]: https://github.com/marcoM32
 [2]: http://www.openstreetmap.org
 [3]: https://github.com/billthefarmer/diary
 [4]: https://developer.android.com/reference/java/util/regex/Pattern.html
 [5]: https://developer.android.com/reference/java/util/regex/Matcher.html
 [6]: https://developer.android.com/reference/java/util/regex/Matcher.html#appendReplacement(java.lang.StringBuffer,%20java.lang.String)
 [7]: https://developer.android.com/reference/java/util/regex/Matcher.html#appendTail(java.lang.StringBuffer)
 
