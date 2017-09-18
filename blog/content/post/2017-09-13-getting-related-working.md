---
title: Getting Related Content working
author: Bill Farmer
type: post
date: 2017-09-13T11:30:00+00:00
url: /getting-related-content-working/
categories:
  - Hacking
keywords:
  - related
  - content
  - hugo
---

The latest release of [Hugo][1] includes [Related Content][2], which I
thought would be nice to add to my blog. However the docs, although
they show you how to set it up, don't explain how it works. To find
that out, I had to look at the various threads on the
[Hugo forums][3].

There are four parts - you need to create a new partial template in
`layouts/partials/related.html` containing something like this:

```html
{{ $related := .Site.RegularPages.Related . | first 5 }}
{{ with $related }}
<hr>
<h3>See Also</h3>
<ul>
  {{ range . }}
  <li><a href="{{ .RelPermalink }}">{{ safeHTML .Title }}</a></li>
  {{ end }}
</ul>
{{ end }}
```

You need to add a reference to your new partial template in your single page template in `layouts/_default/single.html` possibly near the end:

```html
    {{ partial "related" . }}
```

You need to add some configuration options to your config file:

```yaml
related:
  threshold: 10
  toLower: true
  indices:
    - name: keywords
      weight: 150
```

You need to add some keywords to the front matter of your posts. I
tried tags initially, but couldn't get it to work, so I tried
keywords:

```yaml
---
title: Handling run time changes in Android
author: Bill Farmer
type: post
date: 2017-01-08T18:49:31+00:00
url: /handling-run-time-changes-in-android/
categories:
  - Hacking
keywords:
  - android
  - configuration
  - fragment
  - singleton
---
```

You then have to go through all your posts and add keywords to the
front matter.

[1]: https://github.com/gohugoio/hugo/releases
[2]: http://gohugo.io/content-management/related
[3]: https://discourse.gohugo.io
