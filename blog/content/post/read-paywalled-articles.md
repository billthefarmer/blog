---
title: Read Paywalled Newspaper Articles
author: Bill Farmer
type: post
date: 2021-12-18T13:55:26Z
url: /read-paywalled-articles/
categories:
    - Hacking
keywords:
    - paywall
    - newspaper
    - article
---

Most mainstream newspapers show a teaser front page on their web site,
but hide most of their content behind a paywall. As with most things
you can think of, there is a browser extension for this &ndash;
[Bypass Paywalls][1].

However this does not work for all newspapers, the extension appears
to have dropped support for [The Times][2], for example. It is still
possible to read some articles by making use of a search engine to
find another source for the text of the item. To do this:

 * Click on the link for the article, you will see the first paragraph
   or so, with the rest not available without an account.
 * Double click on the title of the article to select all of the
   title.
 * Right click on the selected title to pop up a context menu, then
   select 'Search {search engine} for "Title of Article"'.

![Tanks](images/2021/12/Tanks.png)

 * From the results, if it appears, select the result that begins
   'https://epaper.{newspaper url}...'.
   
 ![Search](images/2021/12/Search.png)
 
 * A page will appear that shows the text of the article as a single
   block of text, difficult to read.
   
 ![Epaper](images/2021/12/Epaper.png)

 * Use the reader view feature of the browser to read the
   article. This, in the current [Firefox][3], is a button in the toolbar.

![Reader.png](images/2021/12/Reader.png)
 

[1]: https://github.com/iamadamdev/bypass-paywalls-chrome/blob/master/README.md
[2]: https://www.thetimes.co.uk/
[3]: https://www.mozilla.org/en-GB/firefox/new/
