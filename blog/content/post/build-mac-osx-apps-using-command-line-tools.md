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
  - swift
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
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
  "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
  <dict>
    <key>CFBundleDevelopmentRegion</key>
    <string>English</string>
    <key>CFBundleExecutable</key>
    <string>LMS</string>
    <key>CFBundleGetInfoString</key>
    <string>Copyright 2011 Bill Farmer</string>
    <key>CFBundleIconFile</key>
    <string>LMS</string>
    <key>CFBundleIdentifier</key>
    <string>com.billthefarmer.LMS</string>
    <key>CFBundleInfoDictionaryVersion</key>
    <string>6.0</string>
    <key>CFBundleName</key>
    <string>LMS</string>
    <key>CFBundlePackageType</key>
    <string>APPL</string>
    <key>CFBundleShortVersionString</key>
    <string>1.0</string>
    <key>CFBundleSignature</key>
    <string>LMS </string>
    <key>CFBundleVersion</key>
    <string>Version 1.0</string>
    <key>LSApplicationCategoryType</key>
    <string>public.app-category.utilities</string>
  </dict>
</plist>

```

The PkgInfo file is just one line, the package type and the app signature concatenated.

`APPLLMS`

![Emacs][2]

### Swift Apps

This can be extended to build [Swift][3] apps using a similar
Makefile. You need to add the swift compiler and a list of your source
files and compiler flags. Xcode needs to be installed to use Swift.

```makefile
  SWIFTC=swiftc
  SOURCES=AppDelegate.swift, <rest of source files>
  FLAGS=-mmacosx-version-min=10.9
```

You don't seem to need `-framework` as Swift seems to sort that out
itself from the import statements in the source files. The
`-mmacosx-version-min=10.9` may be redundant as well. The compile
lines are

```makefile
$(APPNAME): $(SOURCES)
	$(SWIFTC) $(SOURCES) -o $(APPNAME) $(FLAGS)
```

The app and icon building part is identical.

 [1]: https://developer.apple.com/library/prerelease/mac/documentation/DeveloperTools/Conceptual/cross_development/Introduction/Introduction.html#//apple_ref/doc/uid/10000163i
 [2]: images/2016/02/Emacs.png
 [3]: https://www.apple.com/uk/swift
