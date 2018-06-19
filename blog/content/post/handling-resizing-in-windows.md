---
title: Handling Resizing in Windows
author: Bill Farmer
type: post
date: 2018-06-19T20:23:44+01:00
categories:
  - Hacking
keywords:
  - resizing
  - windows
  - c++
  - macOS
  - swift
---

I have a windows app where I want the app window to maintain it's
aspect ratio when it is resized. Windows provides two mechanisms for
this, the [`WM_SIZE`][0] message and the [`WM_SIZING`][1] message. The
`WM_SIZE` message is sent when a window has been resized to allow
child windows to be resized, the `WM_SIZING` message is sent while the
user is resizing it to allow the size to be adjusted. The messages are
handled in the [`WindowProc`][2] callback function.

```C++

// Main window procedure
LRESULT CALLBACK MainWndProc(HWND hWnd,
                             UINT uMsg,
                             WPARAM wParam,
                             LPARAM lParam)
{
    switch (uMsg)
    {
    // ...
	// Size
    case WM_SIZE:
        WindowResize(hWnd, wParam, lParam);
	break;

	// Sizing
    case WM_SIZING:
	    return WindowResizing(hWnd, wParam, lParam);
	break;
    // ...
    }
}
```

Because a window can be zoomed vertically by double clicking on the
window title bar, the `WindowResize` function checks the aspect ratio
and resizes the window if necessary. The `WIDTH` and `HEIGHT`
constants define the desired aspect ratio. If the aspect ratio is OK
the child windows are resized.

```C++

// WindowResize
BOOL WindowResize(HWND hWnd, WPARAM wParam, LPARAM lParam)
{
    int width = LOWORD(lParam);
    int height = HIWORD(lParam) - toolbar.rect.bottom;

    // Get the window and client dimensions
    GetWindowRect(hWnd, &window.wind);
    GetClientRect(hWnd, &window.rect);

    if (width < (height * WIDTH) / HEIGHT)
    {
        // Calculate desired window width and height
        int border = (window.wind.right - window.wind.left) -
            window.rect.right;
        int header = (window.wind.bottom - window.wind.top) -
            window.rect.bottom;
        width = ((height * WIDTH) / HEIGHT) + border;
        height = height + toolbar.rect.bottom + header;

        // Set new dimensions
        SetWindowPos(hWnd, NULL, 0, 0,
                     width, height,
                     SWP_NOMOVE | SWP_NOZORDER);
        return true;
    }

    EnumChildWindows(hWnd, EnumChildProc, lParam);
    return true;
}
```

The `WindowResizing` function checks the offered window size and
adjusts it as necessary depending on which window edge is being
dragged.

```C++

// Window resizing
BOOL WindowResizing(HWND hWnd, WPARAM wParam, LPARAM lParam)
{
    PRECT rectp = (PRECT)lParam;

    // Get the window and client dimensions
    GetWindowRect(hWnd, &window.wind);
    GetClientRect(hWnd, &window.rect);

    // Edges
    int border = (window.wind.right - window.wind.left) -
        window.rect.right;
    int header = (window.wind.bottom - window.wind.top) -
        window.rect.bottom;

    // Window minimum width and height
    int width  = WIDTH + border;
    int height = HEIGHT + toolbar.rect.bottom + header;

    // Minimum size
    if (rectp->right - rectp->left < width)
	rectp->right = rectp->left + width;

    if (rectp->bottom - rectp->top < height)
	rectp->bottom = rectp->top + height;

    // Maximum width
    if (rectp->right - rectp->left > STEP + border)
        rectp->right = rectp->left + STEP + border;

    // Offered width and height
    width = rectp->right - rectp->left;
    height = rectp->bottom - rectp->top;

    switch (wParam)
    {
    case WMSZ_LEFT:
    case WMSZ_RIGHT:
        height = (((width - border) * HEIGHT) / WIDTH) +
            toolbar.rect.bottom + header;
        rectp->bottom = rectp->top + height;
        break;

    case WMSZ_TOP:
    case WMSZ_BOTTOM:
        width = ((((height - toolbar.rect.bottom) - header) *
                  WIDTH) / HEIGHT) + border;
        rectp->right = rectp->left + width;
        break;

    default:
        width = ((((height - toolbar.rect.bottom) - header) *
                  WIDTH) / HEIGHT) + border;
        rectp->right = rectp->left + width;
        break;
    }

    return true;
}
```

The `WindowResizing` function works fine, the `WindowResize` function
handles most cases OK, but isn't completely idiot proof. By contrast
[macOS][3] makes it easy, you define what you want and it just works.

```Swift
        // Set up window
        window.setContentSize(NSMakeSize(400, 480))
        window.contentMinSize = NSMakeSize(400, 480)
        window.contentAspectRatio = NSMakeSize(1.0, 1.2)
        window.showsResizeIndicator = true
        window.collectionBehavior.insert(.fullScreenNone)
```

 [0]: https://msdn.microsoft.com/en-us/library/windows/desktop/ms632646(v=vs.85).aspx
 [1]: https://msdn.microsoft.com/en-us/library/windows/desktop/ms632647(v=vs.85).aspx
 [2]: https://msdn.microsoft.com/en-us/library/windows/desktop/ms633573(v=vs.85).aspx
 [3]: https://developer.apple.com/documentation/appkit/nswindow
