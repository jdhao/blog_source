---
title: "Mechanical Keyboard Set up For Mac"
date: 2018-10-13T02:32:21+08:00
tags: [keyboard]
categories: [Mac]
---

Recently I bought a MacBook Pro 2018. While it is a good design overall, its
built-in keyboard is not so great AFAIK. So I bought a mechanical keyboard
[ikbc c87](http://www.ikbc.com.tw/c87). The mechanical keyboard is designed for
the Windows operating systems. I have to make a few tweaks to make my typing
experience smooth.

<!--more-->

# Before we begin

I have tested the below settings on my MacBook Pro 2018 with the latest Mojave
system. For older Mac OS, it probably will work too. But I cannot say for sure.

# Switch Win and Alt key

The <kbd>Win</kbd> and <kbd>Alt</kbd> keys in the mechanical keyboard
correspond to <kbd>command</kbd> and <kbd>option</kbd> key in the Mac internal
keyboard, respectively. But the positions of the two keys are slightly
different. In order to make the keyboard work more like Mac internal keyboard,
we can use [Karabiner-Elements](https://github.com/tekezo/Karabiner-Elements)
to switch the key mappings of <kbd>Win</kbd> and <kbd>Alt</kbd>.

After installing Karabiner-Elements, open its preference and choose `Simple
Modifications`. Then, choose your external keyboard as `Target Device`. Click
the `Add item` button to add one mapping. We need to add four mappings in
total, i.e.,

|From key| To key|
|---|---|
|left_alt|left_gui|
|left_gui|left_alt|
|right_alt|right_gui|
|right_gui|right_alt|

The `left_gui` and `right_gui` keys are equal to `left_command` and
`right_command`.

After this step, you may also consider changing the key caps of <kbd>Win</kbd>
and <kbd>Alt</kbd> to make it clear you have switched the functions of <kbd>Alt</kbd>
and <kbd>Win</kbd>.

# Disable internal keyboard when external keyboard is connected

While using the external keyboard, you may want to disable the internal
keyboard temporarily.

First, open Karabiner settings and go to `Devices` menu. In the bottom section,
check the button of external USB keyboard to disable the built-in keyboard when
external keyboard is connected.

# Make `Home` and `End` key behave like Windows

On Mac, by default, if you press <kbd>Home</kbd> and <kbd>End</kbd> key, you
will go to the beginning and end of current document, which is often not what
you expect. We can make the behavior of <kbd>Home</kbd> and <kbd>End</kbd> key
like Windows, i.e., go to the beginning and end of current line.

First, create a folder `~/Library/KeyBindings/` if it does not exist. Then
create a file named `DefaultKeyBinding.dict` and add the following settings
into it:

```
{
  "\UF729"  = moveToBeginningOfLine:; // home
  "\UF72B"  = moveToEndOfLine:; // end
  "$\UF729" = moveToBeginningOfLineAndModifySelection:; // shift-home
  "$\UF72B" = moveToEndOfLineAndModifySelection:; // shift-end
  "^\UF729" = moveToBeginningOfDocument:; // ctrl-home
  "^\UF72B" = moveToEndOfDocument:; // ctrl-end
  "^$\UF729" = moveToBeginningOfDocumentAndModifySelection:; // ctrl-shift-home
  "^$\UF72B" = moveToEndOfDocumentAndModifySelection:; // ctrl-shift-end
}
```

Rebooting is needed. The change should take effect after system restart.

Now, happy typing!

# References

+ https://damieng.com/blog/2015/04/24/make-home-end-keys-behave-like-windows-on-mac-os-x
+ https://groups.google.com/forum/#!topic/osx-karabiner/k-Yp6IpIovg
+ https://apple.stackexchange.com/a/16137/285595
+ [The cocoa text system that Mac uses](http://www.hcs.harvard.edu/~jrus/site/cocoa-text.html)
+ [Disable Mac internal keyboard](https://github.com/tekezo/Karabiner-Elements/issues/334#issuecomment-258679549)
