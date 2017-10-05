---
title: Resurrecting a netbook as a web server
author: Bill Farmer
type: post
date: 2016-02-27T19:58:47+00:00
url: /resurrecting-a-netbook-as-a-web-server/
categories:
  - Hacking
keywords:
  - netbook
  - linux
  - mint
  - webtrees
  - apache
  - php
  - mysql
---
When I got fed up with Windows XP on my netbook, I tried windows 7 on it, but it was barely usable. So I tried Windows 7 Starter which was better. I upgraded it to Home Premium, which was OK at first, but slowed down as it got clagged up with updates.

That was it until recently, when I read an article about putting Windows 10 on one. I didn&rsquo;t try that, but I did try [Linux Mint][1] on it. That works quite nicely. Then my other half decided to research her family tree and wanted some software to put her data on. We looked at what was available and decided that [Webtrees][2] was the most usable. That requires a web server and up stepped my netbook.

Installing Apache2, PHP5 and MySql is a doddle on Linux, it all just works, the installer does the configuration required. And Webtrees works out of the box as well, once you&rsquo;ve done the initial configuration.

![Webtrees][3]

Having done all that, and my other half has put her data in, the software will show nice displays of part of the tree, but what it won&rsquo;t do is produce a large format chart of the whole tree that you can print and stick on the wall. I did a bit of investigation into that and couldn&rsquo;t find anything, so I thought I would have a go myself. It turns out that producing a plot of a large complex tree automatically is a very hard problem. So I simplified it with the user filling in the positions in a text file which the program reads to position the  individuals on the chart. The program draws the lines between the generations automatically.

![Whatamess][4]

The project is on [Github][5]. If you change the algorithm for resolving cross references in the input file half way through the development process, you can generate a wonderful mess. All these lines are correct, just the people are in the wrong places.

 [1]: http://www.linuxmint.com
 [2]: https://www.webtrees.net/index.php/en
 [3]: images/2016/02/Webtrees.png
 [4]: images/2016/02/whatamess.png
 [5]: https://github.com/billthefarmer/gpdf
