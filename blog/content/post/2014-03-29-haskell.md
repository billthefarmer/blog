---
title: Haskell
author: Bill Farmer
type: post
date: 2014-03-29T15:26:14+00:00
url: /haskell/
categories:
  - Hacking
keywords:
  - haskell
  - gtk
---
I read an article recently about how amazingly efficient functional programming, and Haskell in particular is. So I thought I&rsquo;s give it a go and see how I got on.

You can try it out in your browser: http://tryhaskell.org

```haskell
    λ let factorial n = product [1..n] in factorial 99
    9332621544394415268169923885626670049071596826438162146859
    2963895217599993229915608941463976156518286253697920827223
    7582511852109168640000000000000000000000
    :: (Enum a, Num a) => a
    λ  
```

Pretty impressive! So I thought I&rsquo;d try a simple web application.

```haskell
    import Network.CGI
    cgiMain :: CGI CGIResult
    cgiMain = do
        setHeader "Content-Type" "text/html; charset=UTF-8"
        output "Hello World!\n"
    main :: IO ()
    main = runCGI (handleErrors cgiMain)
```

This seemed to work, so I tried a graphical GTK app.

```haskell
    import Graphics.UI.Gtk
    main :: IO ()
    main = do
        initGUI
        window <- windowNew
        vbox <- vBoxNew False 10
        hbox <- hBoxNew False 0
        button <- buttonNew
        pixbuf <- pixbufNewFromFileAtSize "941118.jpg" 480 360
        image <- imageNewFromPixbuf pixbuf
        quit <- buttonNew
        set window [ containerBorderWidth := 10,
                     containerChild := vbox ]
        boxPackStart hbox button PackNatural 0
        boxPackStart vbox hbox PackNatural 0
        boxPackStart vbox image PackNatural 0
        hbox <- hBoxNew False 0
        boxPackEnd hbox quit PackNatural 0
        boxPackEnd vbox hbox PackNatural 0
        set button [ buttonLabel := " Hello World " ]
        set quit [ buttonLabel := "  Quit  " ]
        onClicked button (putStrLn "Hello World")
        onClicked quit mainQuit
        onDestroy window mainQuit
        widgetShowAll window
        mainGUI
```

![Gtk](images/2014/03/gtk.png)

However I can&rsquo;t see any real difference between this and doing it the usual step by step procedural way. I even tried reusing a variable to see if it objected. The other problem is that the documentation is written in mathematical jargon which is difficult to understand. for example, the docs for the constructor function pixbufNewFromFileAtSize is:

```haskell
    pixbufNewFromFileAtSize :: String -> Int -> Int -> IO Pixbuf
```

I interpret that to mean: Take the function pixbufNewFromFileAtSize and give it a string and two integers and after a bit of IO you should get a pixbuf, which is reverse Polish to me.

## Update

I tested this on Arch Linux. I found I couldn&rsquo;t update the system with Haskell installed because of dependency errors, so I had to remove it.
