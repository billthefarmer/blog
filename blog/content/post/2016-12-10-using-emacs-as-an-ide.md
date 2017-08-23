---
title: Using Emacs as an IDE
author: Bill Farmer
type: post
date: 2016-12-10T13:59:22+00:00
url: /using-emacs-as-an-ide/
categories:
  - Hacking

---
I had to learn Emacs on Unix many years ago as it was at that time the only development environment for Smallworld Magik. Having learnt it, I have stuck with it as it provides a consistent platform for editing source code and building applications in as many computer languages and platforms as I have used and many more I haven&#8217;t. It is the only source code editor I have used that always gets the indentation and syntax highlighting right.

![C][1]

There is an initial steep learning curve to Emacs, it can be a bit like Unix ed, once you get stuck, you can&#8217;t escape, especially on the command line. The thing to remember is Control-G &#8211; Quit. That will get you out of almost anything. Useful keyboard shortcuts to get started in no particular order:

  * Control-g &#8211; Quit
  * Control-x->Control-c &#8211; Quit Emacs &#8211; it will prompt if you have unsaved changes.
  * Control-s &#8211; Incremental search &#8211; type in what you&#8217;re looking for and keep pressing Control-S until you find the instance you want.
  * Control-r &#8211; The same thing backwards &#8211; you can switch between the two.
  * Alt-x &#8211; Type in a command that isn&#8217;t on the menu &#8211; Control-G will get you out.
  * Control-x->Control-f &#8211; Open a new or existing file &#8211; Emacs calls it visiting a file for some reason.
  * Control-x->Control-s &#8211; Save the current buffer &#8211; the one you are editing.
  * Control-x->Control-w &#8211; Save the current buffer with a new name.
  * Shift-Alt-% &#8211; Query replace &#8211; Prompts for two strings and does a search and replace forward from the current point, prompting for each replacement. Control-G is your friend again.
  * Shift-Control-Alt-% &#8211; Query replace regexp &#8211; the same thing with a regular expression instead.
  * Alt-x->delete-rectangle &#8211; Delete a rectangular area of text &#8211; useful for editing log files or lists.

Some of these things are on the menu, but the keyboard shortcuts are easier to use. You need to customise Emacs to do some of the things I use it for.

![Custom][2]

There are several ways of customising Emacs, but it all ends up in your ~/.emacs config file. First off, you need to install the Emacs packages you need, like groovy-mode for editing Android Gradle build files, JS3-mode for Javascript files, Markdown-mode for Github README files, yaml-mode for Travis Cl build files, etc. This is on the Options->Manage Emacs Packages menu. You can change some settings on the Options menu, like Options->Use CUA keys (Cut/Paste with C-x/C-c/C-v), but remember to Options->Save Options before you quit. Some of them are buried deeply in Options->Customise Emacs and can be a pain to find. It&#8217;s easier to just put them in your ~/.emacs file. Examples are:

<pre>
    '(c-basic-offset 4)
    '(c-offsets-alist (quote ((substatement-open . 0))))
</pre>

That gives you an indent of four spaces for all code editing, and makes it put the opening bracket of if or for statements in the right place.

<pre>
    (add-to-list 'auto-mode-alist '("\\.c\\'" . c++-mode))
    (add-to-list 'auto-mode-alist '("\\.h\\'" . c++-mode))
    (add-hook 'nxml-mode-hook
          (lambda ()
            (set (make-local-variable 'compile-command)
             "ant debug")))
    (add-to-list 'auto-mode-alist '("\\.gradle\\'" . groovy-mode))
    (add-hook 'groovy-mode-hook
          (lambda ()
            (set (make-local-variable 'compile-command)
             "gradle build")))
</pre>

The above Lisp code makes Emacs use c++mode for .c and .h source files, change the compile command to &#8216;ant debug&#8217; for Android xml files, use groovy-mode for .gradle files and change the compile command to &#8216;gradle build&#8217; for Android gradle files.

To use some of the options on the Tools menu, like diff, compile, egrep, etc., require the tools to be in your path. You can achieve this on windows by installing MSYS2 and making sure your path picks it up. Linux and OSX will have these tools installed if you are using it for development.

![Gradle][3]
Build an Android app using Gradle

![Ant][4]
Build an Android app using Apache Ant

It will also edit HTML and just about anything else.

![Java][5]
Java and HTML files

![Error][6]
Find errors

If you click on compiler error messages it will take you to the point in the source file. This works well with C and C++, but not consistently on Android Java source files due to a missing blank in the error message.

 [1]: images/2016/12/C.png
 [2]: images/2016/12/Custom.png
 [3]: images/2016/12/Gradle.png
 [4]: images/2016/12/Ant.png
 [5]: images/2016/12/Java.png
 [6]: images/2016/12/Error.png
