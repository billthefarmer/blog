---
title: Resurrecting an Old Dos App
author: Bill Farmer
type: post
date: 2020-08-09T21:24:46+01:00
url: /resurrecting-dos-app/
categories:
  - Hacking
keywords:
  - c
  - dos
  - console
  - windows
---

Back in the late 90's I wrote an simulation app for an old telecontrol
system that was being superceded. It was written in Turbo C to run on
a DOS or Win95 PC.

The app used a conio library provided by the compiler to present
colour character text and graphics on a monitor using a standard PC
keyboard.

It used to run under 32 bit windows but wouldn't run under 64 bit
windows as there is no DOS emulation. So I decided to see if I could
resurrect it using the windows API. There is a section in the windows
docs about [Console Applications][1] which provides much the same
functionality as the conio library.

Rather than change all the function calls in the app I decided to
write a shim which emulated the conio library and passes the calls on
to the console functions. Most of it is fairly straight forward, there
were slight differences in the cursor addressing, but keyboard input
needed some finessing.

## Keyboard input
The conio library provided `kbhit()` and `getch` functions. the
`getch` function returned the character code for normal keys, and a
zero followed by a code for special and function keys. Windows
provides [GetNumberOfConsoleInputEvents()][2] and [ReadConsoleInput()][3]
functions which offer similar functionality.

However, not all the events offered are keyboard events, and both
keydown and keyup events are presented. The following two functions
deal with this.

```c
// kbhit
int kbhit()
{
    DWORD n;
    INPUT_RECORD record;

    // Wait for something to happen
    WaitForSingleObject(hConsoleInput, TIMER);
    GetNumberOfConsoleInputEvents(hConsoleInput, &n);

    if (n > 0)
    {
        // Ignore unwanted events
        PeekConsoleInput(hConsoleInput, &record, 1, &n);
        if ((record.EventType !=  KEY_EVENT) ||
            (record.Event.KeyEvent.wVirtualKeyCode == VK_SHIFT) ||
            (record.Event.KeyEvent.wVirtualKeyCode == VK_CONTROL) ||
            (record.Event.KeyEvent.wVirtualKeyCode == VK_MENU) ||
            (record.Event.KeyEvent.wVirtualKeyCode == VK_CAPITAL) ||
            // And key up events
            (!record.Event.KeyEvent.bKeyDown))
            ReadConsoleInput(hConsoleInput, &record, 1, &n);

        // Update the event count
        GetNumberOfConsoleInputEvents(hConsoleInput, &n);
    }

    return n;
}
```

The app is in a loop continually calling `kbhit()` and processing keys
and timed events at the same time. so [WaitForSingleObject()][4] with
a timeout is used to drop the CPU usage and still allow the timed
events. Shift, control, alt and caps lock key events are discarded.

```c
int getch()
{
    DWORD n;
    INPUT_RECORD record;
    static BOOL special;

    // If the char is zero, return zero, but don't read event
    PeekConsoleInput(hConsoleInput, &record, 1, &n);
    if (!special && record.Event.KeyEvent.uChar.AsciiChar == 0)
    {
        special = TRUE;
        return 0;
    }

    // Read the event
    ReadConsoleInput(hConsoleInput, &record, 1, &n);
    // Not special, return the char
    if (!special)
        return record.Event.KeyEvent.uChar.AsciiChar;

    special = FALSE;
    switch (record.Event.KeyEvent.dwControlKeyState &
            (SHIFT_PRESSED | LEFT_ALT_PRESSED))
    {
        // Special, return key code
    case 0:
        return record.Event.KeyEvent.wVirtualKeyCode;

        // Special, shift pressed, return key code plus shift
    case SHIFT_PRESSED:
        return record.Event.KeyEvent.wVirtualKeyCode + VK_SHIFT;

        // Special, alt pressed, return key code plus shift
    case LEFT_ALT_PRESSED:
        return record.Event.KeyEvent.wVirtualKeyCode + VK_SHIFT * 2;

        // Special, shift and alt pressed, return key code plus shift
    case SHIFT_PRESSED | LEFT_ALT_PRESSED:
        return record.Event.KeyEvent.wVirtualKeyCode + VK_SHIFT * 3;
    }
}
```

If the key is not a normal key, return zero, set a flag, and don't
read the event. Next time around read the event and return the
code. The conio library return different codes for special keys if the
shit or alt keys are pressed and the app uses this, so add an offset
if this is the case.

![Cadec][9]

## Console output

The console window which pops up when starting a console app is the
wrong dimensions for this app which was written for full screen, which
appears not to be available except by typing `Alt-Enter` into an
existing console window. The [SetConsoleWindowInfo][5] and
[SetConsoleScreenBufferSize][6] functions would appear to fix this,
but in practice didn't work. It was necessary to create a new screen
buffer with [CreateConsoleScreenBuffer][7] and set it active with
[SetConsoleActiveScreenBuffer][8]. Doing this ensures the screen
buffer is the right size and gets rid of any scroll bars. Resizing the
window or making it full screen destroys the existing display, but
subsequent output is positioned correctly.

```c

// textmode
void textmode()
{
    CONSOLE_SCREEN_BUFFER_INFO info;
    HANDLE handle;
    DWORD mode;

    // Do it once
    if (hConsoleOutput == NULL)
    {
        // Disable Ctrl-C
        SetConsoleCtrlHandler(NULL, TRUE);

        // Get handles
        hConsoleOutput = GetStdHandle(STD_OUTPUT_HANDLE);
        hConsoleInput = GetStdHandle(STD_INPUT_HANDLE);

        // Update console mode
        GetConsoleMode(hConsoleOutput, &mode);
        SetConsoleMode(hConsoleOutput, mode | ENABLE_PROCESSED_OUTPUT);
        GetConsoleMode(hConsoleInput, &mode);
        SetConsoleMode(hConsoleInput, mode & !ENABLE_ECHO_INPUT);

        // Resize window
        GetConsoleScreenBufferInfo(hConsoleOutput, &info);
        info.srWindow.Right = info.srWindow.Left + WIDTH;
        info.srWindow.Bottom = info.srWindow.Top + HEIGHT;
        SetConsoleWindowInfo(hConsoleOutput, TRUE, &info.srWindow);

        // Save handle
        handle = hConsoleOutput;

        // Create new screen buffer to get rid of scroll bars
        hConsoleOutput =
            CreateConsoleScreenBuffer(GENERIC_READ | GENERIC_WRITE, 0,
                                      NULL, CONSOLE_TEXTMODE_BUFFER,
                                      NULL);
        SetConsoleActiveScreenBuffer(hConsoleOutput);

        // Set code page
        SetConsoleOutputCP(437);

        // Close old handle
        CloseHandle(handle);
    }
}
```

To tidy up, save the old handle and close it when done.

 [1]: https://docs.microsoft.com/en-us/windows/console
 [2]: https://docs.microsoft.com/en-us/windows/console/getnumberofconsoleinputevents
 [3]: https://docs.microsoft.com/en-us/windows/console/readconsoleinput
 [4]: https://docs.microsoft.com/en-us/windows/win32/api/synchapi/nf-synchapi-waitforsingleobject
 [5]: https://docs.microsoft.com/en-us/windows/console/setconsolewindowinfo
 [6]: https://docs.microsoft.com/en-us/windows/console/setconsolescreenbuffersize
 [7]: https://docs.microsoft.com/en-us/windows/console/createconsolescreenbuffer
 [8]: https://docs.microsoft.com/en-us/windows/console/setconsoleactivescreenbuffer
 [9]: images/2020/08/Cadec.png
 
