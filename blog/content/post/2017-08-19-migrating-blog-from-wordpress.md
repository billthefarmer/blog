---
title: Migrating this blog from Wordpress
author: Bill Farmer
type: post
date: 2017-08-19T14:30:00+00:00
url: /migrating-blog-from-wordpress/
categories:
  - Hacking

---

I have got fed up with [Wordpress][1]. It required constant updates,
which doesn't work automatically on [Sourceforge developer web][2],
and it's slow, especially in admin mode. I came across a reference to
[Hugo][3] while following up on something unrelated, so I decided to
investigate.

I looked for migration tools, and found two:

 * [Exitwp][4], which reads a Wordpress export xml file and converts
   to [Jeckyll][5]
 * [WordPress to Hugo Exporter][6], which is a Wordpress plugin and
   exports to [Hugo][3]

I found a [blog post][7] from [Justin Dunham][8] documenting migrating
his blog. He tried the [WordPress to Hugo Exporter][6] first, but
couldn't get it to work, so he used [Exitwp][4]. I tried this, which
just worked, but I didn't like the look of tidying up all the blog
entries to work with [Hugo][3].

So I installed the exporter, which refused to activate, due to an
error. I can't remember how to find the PHP log, so I tried it from
the command line, which gave a useful error message. I looked up the
error on the exporter issues page, and found Sourceforge was using an
old version of PHP. So I looked at the PHP code, and found it was
using square brackets for an array, which I'd found an annoyance while
writing a Wordpress plugin.

```PHP
        if (in_array($post->post_status, ['draft', 'private'])) {
```

So I changed it to the old style syntax, and found that was it, it
just worked. So change one line to make it work with old PHP versions.

```PHP
        if (in_array($post->post_status, array('draft', 'private'))) {
```

Except it didn't, I couldn't find the output, not where it said it was
anyway. So I used the plugin from the admin page instead, which did
work. The output looked pretty good, except for making a right pig's
ear of image references and some links. And for some reason it left
the first blog page in HTML.

It did use the very neat Markdown syntax for some of the links
`[Hugo][1]` with the list of urls at the end of the page `[1]:
http://gohugo.io`.

Installing [Hugo][3] and getting it working was not a problem, there's
a [Quick Start][9] page with links to the bits you need. The next
thing was to choose a theme, which was a problem, because Hugo's
[Themes][10] page seems to be down. I had to use the
[Wayback Machine][11] to find a theme. I settled on
[Theme Hugo Foundation6][12] which looked fairly similar to the
Wordpress theme I was using.

The example config file for the theme is written in [TOML][13], which
I had not seen before. The config file for the export was in
[YAML][14], which I already use, so I needed to translate. The TOML
example file contains some obscure constructions which I certainly
didn't know the YAML equivalent for, so I looked for a converter and
found [remarshal][15]. I've got both [MSYS2][16] and [Git][17]
installed on my Windows box, so I've got a bash unix like environment
to run [Python][18] utilities in, so installing the prerequisites for
[remarshal][15] and getting it working was not a problem.

So now I've got a config file which does what I want it to do:

```yaml
baseURL: http://billthefarmer.github.io/blog/
languageCode: en-gb
title: Bill Farmer's Blog
copyright: Copyright &copy; 2017 Bill Farmer
theme: theme-hugo-foundation6
publishDir: d:/billthefarmer.github.io/blog/
pygmentscodefences: true

# number of posts to show on home/category/tag pages (Default: 10)
paginate: 5

taxonomies:
  category: categories
  # tag: tags

permalinks:
  page: /:slug/
  post: /:year/:month/:title/

menu:
  sidebar:
    - name: Bill Farmer
      url: http://billthefarmer.github.io
    - name: Github
      url: https://github.com/billthefarmer

params:
  description: Random thoughts on random subjects
  header_background: images/2013/12/cropped-P1010089.jpg
  sidebar:
    # Optional about block for sidebar (can be Markdown)
    # about: Bill Farmer's Blog
    num_recent_posts: 5
  sharingicons:
    hide: true
...
```

The useful bits are `copyright: Copyright &copy; 2017 Bill Farmer`,
which overrides the page footer, `publishDir:
d:/billthefarmer.github.io/blog/`, which puts the output where I want
it and `# tag: tags`, which stops generating a tags page. It was
necessary to add the `sidebar` entry to stop an error, so the
`num_recent_posts` item is redundant, but needed because I don't want
the `about` item. I had to look at the source template to work that
out.

I wanted a background image for the page header like on my original
blog. To get that to work was quite complex. There aren't any hooks
for modifying the blog header, but there is for the page `<head>`. So
I copied the `head-custom.html` from the `layouts/partials` folder in
the theme to `layouts/partials` in the blog source and modified it
with some extra styles.

```html
<!--
    Create <project-root>/layouts/partials/head-custom.html to
    overwrite this empty template and put custom code into the <head>
    section.
  -->

<style>
.callout.large {
    background-image: url({{ $.Site.Params.header_background }});
    background-size: cover;
    background-position: center bottom;
    background-repeat: no-repeat;
}
</style>
```

Reading the documentation on [variables][20], I put an entry in the
config file `header_background: images/2013/12/cropped-P1010089.jpg`
and put `{{ $.Site.Params.header_background }}` instead of the path in
the styles.

I wanted to get syntax highlighting working, so I installed
[Pygments][19]. It's on the path, it works, but Hugo can't find it.

```
WARN 2017/08/19 16:42:47 Highlighting requires Pygments to be installed and in the path
```

I shall have to try it on Linux to see if it works there, which it
does, but the style is horrible. I changed it to `default`.

To get the highlighting working in windows I had to install real
windows python and install Pygments there.

I found a template on the [Taxonomy Variables][21] page to generate a
list of blog pages.

```html
<section>
  <ul>
    {{ range $taxonomyname, $taxonomy := .Site.Taxonomies }}
      <li><a href="{{ "/" | relLangURL}}{{ $taxonomyname | urlize }}">{{ $taxonomyname }}</a>
        <ul>
          {{ range $key, $value := $taxonomy }}
          <li> {{ $key }} </li>
                <ul>
                {{ range $value.Pages }}
                    <li><a href="{{ .Permalink}}"> {{ .LinkTitle }} </a> </li>
                {{ end }}
                </ul>
          {{ end }}
        </ul>
      </li>
    {{ end }}
  </ul>
</section>
```

After hacking that about and putting it in
`layouts/shortcodes/site-index.html`, and putting a shortcode `{{</*
site-index */>}}` in the index page, I got a reasonable site index.

```html
<section>
  {{ range $key, $value := .Site.Taxonomies.categories }}
  <h3>{{ title $key }}</h3>
  <ul>
    {{ range $value.Pages.ByTitle }}
    <li><a href="{{ .Permalink }}">{{ safeHTML .LinkTitle }}</a></li>
    {{ end }}
  </ul>
  {{ end }}
</section>
```

There were a couple of extra tweaks that took a long time to find in
the docs. The `key` for each category is in lower case, and I wanted
it capitalised. I found `upper` and `lower`, tried searching for
'capital' and got nothing useful. Eventually I found it somewhere in
the template docs as a throw away reference: [`title`][22]. Wordpress
put curly quote characters in my posts and post titles and they were
showing up as `&#8217;` in the post titles in the index. I found
`safeHTML` in the same place which tidied that up.

 [1]: https://wordpress.org
 [2]: https://sourceforge.net/p/forge/documentation/Developer%20Web%20Services
 [3]: http://gohugo.io
 [4]: https://github.com/thomasf/exitwp
 [5]: https://github.com/jekyll/jekyll
 [6]: https://github.com/SchumacherFM/wordpress-to-hugo-exporter
 [7]: http://justindunham.net/migrating-from-wordpress-to-hugo
 [8]: http://justindunham.net
 [9]: http://gohugo.io/getting-started/quick-start
 [10]: https://themes.gohugo.io
 [11]: https://archive.org/web/web.php
 [12]: https://github.com/htkoca/theme-hugo-foundation6
 [13]: https://github.com/toml-lang/toml
 [14]: http://yaml.org
 [15]: https://github.com/dbohdan/remarshal
 [16]: http://www.msys2.org
 [17]: https://git-scm.com
 [18]: https://www.python.org
 [19]: http://pygments.org
 [20]: http://gohugo.io/variables
 [21]: https://gohugo.io/variables/taxonomy
 [22]: https://gohugo.io/functions/title
