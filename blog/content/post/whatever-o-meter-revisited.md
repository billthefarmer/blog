---
title: "Whatever-o-meter revisited"
author: Bill Farmer
date: 2017-08-24T11:47:00+01:00
url: /whatever-o-meter-revisited/
categories:
  - Hacking
keywords:
  - hugo
  - wordpress
  - plugin
  - javascript
  - whatever-o-meter
  - php
---

I wrote the whatever-o-meter about five years ago in PHP and
Javascript as a Wordpress plugin. As I have migrated my blog to Hugo,
I thought to have a go at reimplementing it. The exporter I used made
a good job of translating the Wordpress custom fields to Hugo front
matter parameters. I had to put dashes in quotes, otherwise they get
interpreted as `[<nil>]`.

```yaml
intro: Please answer the following questions carefully and truthfully.
question:
  - You are lying in bed on Monday morning...
result:
  - '%d percent, Perhaps you should consider emigration.'
  - '%d%, Consider taking a long holiday somewhere far away.'
left:
  - Go and jump in the shower quick
right:
  - Jump on him
weights: 2,1,1,1,1
centre:
  - '-'
  - '-'
  - So so
more: https://billthefarmer.github.io/blog/morris-o-meter
```

The javascript part should just work as it is, the PHP plugin needed
re-implementing as a hugo shortcode. The first part of the plugin just
outputs svg code to draw the dial and pointer, that was fairly easy to
implement, it just has some iteration in it. I had to change the ticks
slightly as Hugo doesn't appear to support floating point arithmetic
in templates.

The really difficult part was reading the page parameters, as most of
them are optional, and some of the arrays can have bits missing. PHP
just coped with that, anything not defined just becomes `null`. In
Hugo I had to make a great deal of use of `default` and `slice` to
create default values.

```html
{{ with .Page.Params }}
  <div id="first" style="text-align: center">
    <h4>{{ .intro }}</h4>
    <input type="button" value="Start" class="start" id="start" />
  </div>
  {{ $questions := .question }}
  {{ $left_texts := default (slice "No") .left }}
  {{ $centre_texts := default (slice "-") (default .centre .center) }}
  {{ $right_texts := default (slice "Yes") .right }}
  {{ $last_index := sub (len $questions) 1 }}
  {{ range $index, $question := $questions }}
  {{ $left := default "No" (index $left_texts $index) }}
  {{ $centre := default "-" (index $centre_texts $index) }}
  {{ $right := default "Yes" (index $right_texts $index) }}
```

The other difficult part was extracting optional parameters to pass as
json to the javascript. I had to use `Scratch` inside with blocks to
save values. Luckily `Scratch.Get` returns `null` if the key doesn't
exist.

```html
{{ with .result }}
{{ $.Scratch.Set "results" . }}
{{ end }}
{{ with (default .colors .colours) }}
{{ $.Scratch.Set "colours" (split . ",") }}
{{ end }}
{{ with .weights }}
{{ $.Scratch.Set "weights" (split . ",") }}
{{ end }}
{{ with .duration }}
{{ $.Scratch.Set "duration" . }}
{{ end }}
{{ with .easing }}
{{ $.Scratch.Set "easing" . }}
{{ end }}
{{ $json := dict "results" ($.Scratch.Get "results") "colours" ($.Scratch.Get "colours")
                 "weights" ($.Scratch.Get "weights") "duration" ($.Scratch.Get "duration")
                 "easing" ($.Scratch.Get "easing") }}
<script type="text/javascript">
  var whatever_data = {{ safeJS (jsonify $json) }};
</script>
{{ end }}
```

So the generated json looks like this, with nulls for the missing keys.

```javascript
<script type="text/javascript">
var whatever_data =
  {"colours":null,"duration":null,"easing":null,"results":["%d percent,
    Perhaps you should consider emigration.",...],"weights":["2","1","1","1","1"]};
</script>
```

The javascript code needed a bit of work to force the weights, which
were passed by Hugo as an array of strings, to be interpreted as numbers.

```javascript
        // Check if weights are defined
        if (weights == null)
        {
            value += $("#value-" + panel).slider("value");
            total++;
        }

        else
        {
            value += $("#value-" + panel).slider("value") *
	        weights[index];
            total += +weights[index];
        }
```
