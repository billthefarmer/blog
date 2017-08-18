---
title: Resurrecting a netbook as a web server
author: Bill Farmer
type: post
date: 2016-02-27T19:58:47+00:00
url: /resurrecting-a-netbook-as-a-web-server/
categories:
  - Hacking

---
When I got fed up with Windows XP on my netbook, I tried windows 7 on it, but it was barely usable. So I tried Windows 7 Starter which was better. I upgraded it to Home Premium, which was OK at first, but slowed down as it got clagged up with updates.

That was it until recently, when I read an article about putting Windows 10 on one. I didn&#8217;t try that, but I did try <a href="http://www.linuxmint.com/" target="_blank">Linux Mint</a> on it. That works quite nicely. Then my other half decided to research her family tree and wanted some software to put her data on. We looked at what was available and decided that <a href="https://www.webtrees.net/index.php/en" target="_blank">Webtrees</a> was the most usable. That requires a web server and up stepped my netbook.

Installing Apache2, PHP5 and MySql is a doddle on Linux, it all just works, the installer does the configuration required. And Webtrees works out of the box as well, once you&#8217;ve done the initial configuration.

<a href="http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/02/Webtrees.png" rel="attachment wp-att-367"><img class="alignnone size-large wp-image-367" src="http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/02/Webtrees-1024x706.png" alt="Webtrees" width="625" height="431" srcset="http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/02/Webtrees-1024x706.png 1024w, http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/02/Webtrees-300x207.png 300w, http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/02/Webtrees-768x529.png 768w, http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/02/Webtrees-624x430.png 624w, http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/02/Webtrees.png 1524w" sizes="(max-width: 625px) 100vw, 625px" /></a>

Having done all that, and my other half has put her data in, the software will show nice displays of part of the tree, but what it won&#8217;t do is produce a large format chart of the whole tree that you can print and stick on the wall. I did a bit of investigation into that and couldn&#8217;t find anything, so I thought I would have a go myself. It turns out that producing a plot of a large complex tree automatically is a very hard problem. So I simplified it with the user filling in the positions in a text file which the program reads to position the  individuals on the chart. The program draws the lines between the generations automatically.

<a href="http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/02/whatamess.png" rel="attachment wp-att-370"><img class="alignnone size-full wp-image-370" src="http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/02/whatamess.png" alt="whatamess" width="640" height="453" srcset="http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/02/whatamess.png 640w, http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/02/whatamess-300x212.png 300w, http://billthefarmer.users.sourceforge.net/wordpress/wp-content/uploads/2016/02/whatamess-624x442.png 624w" sizes="(max-width: 640px) 100vw, 640px" /></a>

The project is on <a href="https://github.com/billthefarmer/gpdf" target="_blank">Github</a>. If you change the algorithm for resolving cross references in the input file half way through the development process, you can generate a wonderful mess. All these lines are correct, just the people are in the wrong places.