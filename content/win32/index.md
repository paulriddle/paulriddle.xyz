+++
title = "Win32 Platform Layer for Video Games"
template = "win32.html"
date = 2020-10-05
updated = 2020-10-05
[extra]
tags = ["c", "handmade hero"]
+++

# Win32 Platform Layer for Video Games

Install clang. That will be the compiler. The minimum requirement for the platform is
Windows 10. From the official Win32 documentation:

> If you are new to Windows programming, it can be disconcerting when you first see a Windows program.

Indeed. It does look messy. Luckily, it is well documented and not *actually* terrible as I,
for some reason, expected.

## Opening a window

Win32 programs start with `WinMain` instead of `main`. Windows generates its own `main`
which does some setup and calls `WinMain`.

### References
---
0. [Handmade Hero](https://handmadehero.org/)
1. [Making a Video Game From Scratch by Ryan Ries](https://www.youtube.com/playlist?list=PLlaINRtydtNWuRfd4Ra3KeD6L9FP_tDE7)
2. [theForger's Win32 API Programming Tutorial](http://www.winprog.org/tutorial/start.html)
3. [Official Win32 Guide](https://docs.microsoft.com/en-us/windows/win32/desktop-programming)
---
