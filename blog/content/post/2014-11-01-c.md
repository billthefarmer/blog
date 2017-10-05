---
title: C++
author: Bill Farmer
type: post
date: 2014-11-01T13:10:54+00:00
url: /c/
categories:
  - Hacking
keywords:
  - c++
  - android
  - sudoku
  - astyle
---
I made a vow about 20 years ago not to attempt to learn C++, when a colleague went on a C++ course. I did buy a C++ book, but it wasn&rsquo;t that useful. I have looked at the source of various C++ programs over the years to attempt to find out how they worked. I have, until recently, invariably found that the code that does something useful is either hidden in include files, or buried so deep in obscure code that just seems to create and do strange things with various objects, that I have been unable to find it. I have seen similar Java programs.

Recently I have looked at some android C++ code, which was partially understandable, and a [Windows C++ project][1] that gets a stream of images of Sudoku puzzles from a webcam, rotates them so the lines line up, finds the outline, OCRs the digits,solves the puzzle and displays the solution, all in real time. And the code makes sense after it&rsquo;s been tidied up a bit. So I have successfully taken the bits I want to use, written some include files to resolve all the windows stuff, and compiled it using the android native tool kit. Whether it works I haven&rsquo;t found out yet. Unfortunately, although it is alleged to be open source, the author hasn&rsquo;t put any licence or copyright info in the source files.

What I also found extremely useful is [AStyle][2]. This is a tool that lets you lay out code the way you like so can read it rather than how it was written. Just type `astyle --style=allman -p *.cpp`.

 [1]: http://www.codeproject.com/Articles/238114/Realtime-Webcam-Sudoku-Solver
 [2]: http://sourceforge.net/projects/astyle
