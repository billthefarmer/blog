---
title: Build Mac OSX apps using command line tools
author: Bill Farmer
type: post
date: 2016-02-02T17:59:09+00:00
url: /build-mac-osx-apps-using-command-line-tools/
categories:
  - Hacking
keywords:
  - xcode
  - tools
  - llvm
  - framework
  - version
  - sips
  - emacs
---
You will need to install the Xcode command line tools, you don&rsquo;t need the Xcode app. The easiest way to do this is to attempt to build an app like emacs by typing `./configure` on the command line. A dialog box will pop up asking if you want to download and install the tools.

Xcode now uses the LLVM CLang compiler and documentation is exceedingly hard to find. You can type `gcc -help` or `clang -help` on the command line to get some info. I found two web pages with more useful info: http://clang.llvm.org/docs/CommandGuide/clang.html and https://developer.apple.com/library/prerelease/mac/documentation/Porting/Conceptual/PortingUnix/compiling/compiling.html.

From this I found some useful compiler switches:

`-framework` _framework_ (in `LDFLAGS`)

Links the executable being built against the listed framework. For example, you might add `-framework vecLib` to include support for vector math.

`-mmacosx-version-min version`

Specifies the version of OS X you are targeting. You must target your compile for the _oldest_ version of OS X on which you want to run the executable. In addition, you should install and use the cross-development SDK for that version of OS X. For more information, see [SDK Compatibility Guide][1].

and

`-mmacosx-version-min=<version>`

When building for Mac OS X, specify the minimum version supported by your application.

The easiest way to build an app is to use a Makefile:

```makefile
RM=rm
MD=mkdir
CP=cp
SIPS=sips

GCC=gcc
CFLAGS=-framework Carbon -framework Audiounit -framework CoreAudio \
		-framework Accelerate -m32 -Wno-deprecated-declarations \
		-mmacosx-version-min=10.6

APPNAME=LMS

APPBUNDLE=$(APPNAME).app
APPBUNDLECONTENTS=$(APPBUNDLE)/Contents
APPBUNDLEMACOS=$(APPBUNDLECONTENTS)/MacOS
APPBUNDLERESOURCES=$(APPBUNDLECONTENTS)/Resources

appbundle: $(APPNAME) $(APPNAME).icns
	$(RM) -rf $(APPBUNDLE)
	$(MD) $(APPBUNDLE)
	$(MD) $(APPBUNDLECONTENTS)
	$(MD) $(APPBUNDLEMACOS)
	$(MD) $(APPBUNDLERESOURCES)
	$(CP) Info.plist $(APPBUNDLECONTENTS)/
	$(CP) PkgInfo $(APPBUNDLECONTENTS)/
	$(CP) $(APPNAME).icns $(APPBUNDLERESOURCES)/
	$(CP) $(APPNAME) $(APPBUNDLEMACOS)/

$(APPNAME): $(APPNAME).c
	$(GCC) $(APPNAME).c -o $(APPNAME) $(CFLAGS)

$(APPNAME).icns: $(APPNAME).png
	$(RM) -rf $(APPNAME).iconset
	$(MD) $(APPNAME).iconset
	$(SIPS) -z 16 16   $(APPNAME).png --out $(APPNAME).iconset/icon_16x16.png
	$(SIPS) -z 32 32   $(APPNAME).png --out $(APPNAME).iconset/icon_16x16@2x.png
	$(SIPS) -z 32 32   $(APPNAME).png --out $(APPNAME).iconset/icon_32x32.png
	$(SIPS) -z 64 64   $(APPNAME).png --out $(APPNAME).iconset/icon_32x32@2x.png
	$(SIPS) -z 128 128 $(APPNAME).png --out $(APPNAME).iconset/icon_128x128.png
	$(SIPS) -z 256 256 $(APPNAME).png --out $(APPNAME).iconset/icon_128x128@2x.png
	$(SIPS) -z 256 256 $(APPNAME).png --out $(APPNAME).iconset/icon_256x256.png
	$(SIPS) -z 512 512 $(APPNAME).png --out $(APPNAME).iconset/icon_256x256@2x.png
	$(SIPS) -z 512 512 $(APPNAME).png --out $(APPNAME).iconset/icon_512x512.png
	$(CP) $(APPNAME).png $(APPNAME).iconset/icon_512x512@2x.png
	iconutil -c icns -o $(APPNAME).icns $(APPNAME).iconset
	$(RM) -r $(APPNAME).iconset

```

This compiles the code, makes an icon file from an image and builds the app structure. The reason for the `-Wno-deprecated-declarations` is that this app was first written on OSX 10.4 using Carbon and I&rsquo;m compiling for 10.6. You need two other text files `Info,plist` and `PkgInfo`.

```xml
&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
  "http://www.apple.com/DTDs/PropertyList-1.0.dtd"&gt;
  &lt;dict&gt;
    &lt;key&gt;CFBundleDevelopmentRegion&lt;/key&gt;
    &lt;string&gt;English&lt;/string&gt;
    &lt;key&gt;CFBundleExecutable&lt;/key&gt;
    &lt;string&gt;LMS&lt;/string&gt;
    &lt;key&gt;CFBundleGetInfoString&lt;/key&gt;
    &lt;string&gt;Copyright 2011 Bill Farmer&lt;/string&gt;
    &lt;key&gt;CFBundleIconFile&lt;/key&gt;
    &lt;string&gt;LMS&lt;/string&gt;
    &lt;key&gt;CFBundleIdentifier&lt;/key&gt;
    &lt;string&gt;com.billthefarmer.LMS&lt;/string&gt;
    &lt;key&gt;CFBundleInfoDictionaryVersion&lt;/key&gt;
    &lt;string&gt;6.0&lt;/string&gt;
    &lt;key&gt;CFBundleName&lt;/key&gt;
    &lt;string&gt;LMS&lt;/string&gt;
    &lt;key&gt;CFBundlePackageType&lt;/key&gt;
    &lt;string&gt;APPL&lt;/string&gt;
    &lt;key&gt;CFBundleShortVersionString&lt;/key&gt;
    &lt;string&gt;1.0&lt;/string&gt;
    &lt;key&gt;CFBundleSignature&lt;/key&gt;
    &lt;string&gt;LMS &lt;/string&gt;
    &lt;key&gt;CFBundleVersion&lt;/key&gt;
    &lt;string&gt;Version 1.0&lt;/string&gt;
    &lt;key&gt;LSApplicationCategoryType&lt;/key&gt;
    &lt;string&gt;public.app-category.utilities&lt;/string&gt;
  &lt;/dict&gt;
&lt;/plist&gt;

```

The PkgInfo file is just one line, the package type and the app signature concatenated.

`APPLLMS`

![Emacs][2]

 [1]: href="https://developer.apple.com/library/prerelease/mac/documentation/DeveloperTools/Conceptual/cross_development/Introduction/Introduction.html#//apple_ref/doc/uid/10000163i
 [2]: images/2016/02/Emacs.png
