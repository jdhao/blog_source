---
title: "IrfanView Settings"
date: 2020-06-20 00:53:59+0800
tags: []
categories: [tools]
---

[IrfanView](https://www.irfanview.com/) is a lightweight, fast and powerful
image viewer for Windows. Here are some settings to make it more useful.

<!--more-->

# Auto-jump to next file after deleting current one

By default, IrfanView will not jump to next image if you delete the current
one. To enable this, click `Options --> Properties/Settings... --> File
Handling`. Under the `delete` section, check the following box:

> Jump to the next file after deleting/moving

# Fit image to window size when viewing

When we are viewing images in a directory, it is annoying that window size
changes as image sizes vary. To keep the window size and fit the image to
window, go to `View --> Display options (window mode)` and choose `Fit images
to window`.

Ref:

+ https://irfanview-forum.de/showthread.php?t=11273

# Do not exit program when pressing ESC

By default, Irfanview will exit immediately if we press <kbd>ESC</kbd> when we
are viewing an image, which is really annoying. To disable this behavior, go to
`Options --> Properties/Settings`, in the popup window, click `Start / Exit
options`. In the `Exit options` section, check the box `Warning on ESC program
exit`. Do not forget to click `OK` button to save your settings.

<p align="center">
<img src="https://blog-resource-1257868508.file.myqcloud.com/20200620010129.png" width="600">
</p>

Ref:

+ https://irfanview-forum.de/showthread.php?t=1092

# Skip txt files when viewing files in a directory

By default, ifranview will also show text files, which is annoying. Here is how
to disable this behavior. Go to `Options -> Properties/Settings -> Extentions`:

+ Check the boxes for the file extensions that we want irfanview to open
+ Select the option "Load only associated types while moving through directory".

Ref:

+ https://superuser.com/q/149910/736190


# Copy current image path quickly

When I check an image with irfanview, often I want to copy the image path to
use in command line. Currently, I have to press `i` (show image properties),
then copy the full image path. This is boring and cumbersome. I found from the
[irfanview forum](https://irfanview-forum.de/archive/index.php/t-1069.html) the
following [AutoHotKey](https://www.autohotkey.com/) script to automate this
process:

<details>
<summary><font size="2" color="red">Click to show the code.</font></summary>

```autohotkey
#NoEnv ; Recommended for performance and compatibility with future AutoHotkey releases.
SendMode Input ; Recommended for new scripts due to its superior speed and reliability.
SetWorkingDir %A_ScriptDir% ; Ensures a consistent starting directory.

; If IV is running, then the script will modify the key combination Ctrl+Shift+C to
; copy file path (oposed to set image as desktop background)

; OPTIONAL: Following code ensure that script terminates when IV close. Delete (or comment out)
; line 10 - 19 for letting the script run until manually stopped by user.
SetTimer, check_window, 500
return
check_window:
IfWinNotExist, ahk_class IrfanView
{
TrayTip, "IrfanView check", IrfanView har being closed. Script terminate.
Sleep 3000
ExitApp, 1
}
return


; RESTRICT HOTKEY TO IRFANVIEW ONLY <BEGIN>
; Ensures that hotkey manipulation works ONLY when IrfanView program window is active (most foremost)
#IfWinActive ahk_class IrfanView
^+c::

WinWait, ahk_class IrfanView, , 5

if errorlevel
{
MsgBox, 0, venter på vindu, IrfanView ikke funnet på 5 sekunder - avslutter
ExitApp, 1
}

; Remove any existing content on clipboard.
clipboard =

WinActivate, ahk_class IrfanView

Sleep 300

; Simulate key press "i" to open image properties dialog box (ipdb).
Send, i
WinWaitActive, IrfanView - Image properties, , 2

; If ipdb for some reason fail to open, user get an error message and script terminate.
if errorlevel
{
MsgBox, 0, Waiting, Image properties window didn't appear. Script terminates now.
ExitApp, 1
}

; Hit tab three times
Send, {TAB 3}
; COpy text (image path) to clipboard
Send, {CTRLDOWN}c{CTRLUP}
; Waiting until clipboard contains data (in most cases this command could be ommitted without any effect)
ClipWait
; Quits the ipdb
Send, {ALTDOWN}o{ALTUP}


; OPTIONAL - Make a tray tip (baloon) appear. User should be confident that image path is actually copied to clipboard
TrayTip, Image path:, %clipboard%, 4
Sleep 4000
HideTrayTip()

HideTrayTip() {
    TrayTip  ; Attempt to hide it the normal way.
    if SubStr(A_OSVersion,1,3) = "10." {
        Menu Tray, NoIcon
        Sleep 200  ; It may be necessary to adjust this sleep.
        Menu Tray, Icon
    }
}

; RESTRICT HOTKEY TO IRFANVIEW ONLY <END>
#IfWinActive
```
</details>

First, we need to install autohotkey. Save the above script in a directory
and name it `copy_im_path_irfanview.ahk`. Open the irfanview window, then
double click the script file to run this script.

When you are viewing an image, press `Ctrl-Shift-C` to copy the image path. A
tooltip will also pop up on the bottom right corner to show the image path
copied. Unfortunately, `Ctrl-shift-C` is also used by Irfanview to set the
desktop wallpaper. You may want to change to other shortcut (change the line
`^+c::` to what you want, for example `^!C::`, i.e., `Ctrl-Alt-C`[^1]).


[^1]: See [https://www.autohotkey.com/docs/Tutorial.htm#s21] on how speical keys such `Ctrl`, `Alt` are represented in AutoHotKey.
