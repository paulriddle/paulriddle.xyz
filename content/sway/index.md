+++
title = "Sway, the tiling window manager"
template = "sway.html"
date = 2020-09-29
updated = 2020-09-29
[extra]
tags = ["linux ricing"]
+++

# Sway, the tiling window manager

I like screen real estate, so I don't use gaps. Sometimes it's difficult to
track where I am. There's a small utility that highlights the window you have
just switched to. It is called [flashfocus](https://github.com/fennerm/flashfocus).

```
yay flashfocus-git
```
And then edit Sway config like this:
```diff
@@ -1,5 +1,6 @@
#
+# Launch it on startup
+exec_always --no-startup-id flashfocus
 set $win Mod4
 set $alt Mod1
+# Highlight current window for a second
+bindsym $win+c exec --no-startup-id flash_window
```
Now reload the config, you have a mapping for that. For me it's
```
bindsym $win+Shift+c reload
```
Then you open a terminal and it blinks. When there's only one terminal in the
workplace you don't want it to blink. flashfocus has a config file located at
```
~/.config/flashfocus/flashfocus.yml
```
Change `flash-lone-windows` from `always` to `never`.
