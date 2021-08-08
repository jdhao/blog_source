---
title: "Sublime Text Tips and Tricks That Will Make Your Life Easier"
date: 2017-10-21 17:34:00 +0800
tags: [Sublime-Text]
categories: [tools]
---

In this post I will share some tips on how to use Sublime Text on Windows.
These tips are collected over time and will be updated frequently
(hopefully).

<!--more-->

# Open and close side bar

You can use <kbd>Ctrl</kbd>+<kbd>K</kbd>+<kbd>B</kbd> to open and close the
side bar. The correct way to use this shortcut: hold on <kbd>Ctrl</kbd>, first
press <kbd>K</kbd> and then press <kbd>B</kbd>.

# Change the case of selected text

To turn the selected text into upper case, use the shortcut key
<kbd>Ctrl</kbd>+<kbd>K</kbd>+<kbd>U</kbd>. Here is how exactly to operate:
first you need to press <kbd>Ctrl</kbd>, then press <kbd>K</kbd> and release
it, finally, you press the <kbd>U</kbd> key, do not release the <kbd>Ctrl</kbd>
key in the process. To turn the selected text into lower case, use the shortcut
key <kbd>Ctrl</kbd>+<kbd>K</kbd>+<kbd>L</kbd>.

To turn the selected text into title case, you need to add a custom key
binding. Open your key binding file and add the following setting:

```yaml
{ "keys": ["ctrl+k", "ctrl+t"], "command": "title_case" }
```

Then you can convert the selected text into title case with the shortcut
<kbd>Ctrl</kbd>+<kbd>K</kbd>+<kbd>T</kbd>.

# Select text between quotation marks

You can use the Plugin
[BracketHighlighter](https://github.com/facelessuser/BracketHighlighter) to
achieve what you want. First, you need to install this Plugin. Then, go to
`Preferences --> Key Bindings`. Add the following key binding setting,

```yaml
{
    "no_outside_adj": null,
    "keys": ["ctrl+alt+super+s"],
    "command": "bh_key",
    "args":
    {
        "lines" : true,
        "plugin":
        {
            "type": ["__all__"],
            "command": "bh_modules.bracketselect"
        }
    }
}
```

Then you can use the shortcut key
<kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>Super</kbd>+<kbd>S</kbd> to select texts
between quotation marks.

# Show current file in side bar

In the current file, right click your mouse. In the pop-up contextual menu,
choose `Reveal in Side Bar` and the file will be highlighted in the side bar.

# Prevent a file from opening in the tab when I right click on it

One annoying thing when using Sublime-Text is that when you want to right click
on a file in the sidebar to call its contextual menu, the file is automatically
opened in the tab. If you want to disable this behaviour, you can add an option
in your user Settings:

```yaml
{"preview_on_click": false,}
```
After using this option, you can left click on a file twice to open it.

# Open a file in the current folder quickly

You can press <kbd>Ctrl</kbd>+<kbd>P</kbd> and start to type the filename. See
the following image,

<img src="https://blog-resource-1257868508.file.myqcloud.com/sublime_find_file_in_folder.png"
         title="search a file in current folder"
         style="float: middle;">

# Find a string in all files in current project

Press <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>F</kbd>, you will see three boxes:
`Find`, `Where` and `Replace`. Type the string you want to
search in `Find` box , and specify the folder you want to search in `Where` box.

The search result will be presented in a page called `Find Results`. If you
want to go to specific file which contains the searched string, double click on
the line.

This tip is based on [a wonderful stackoverflow
post](https://stackoverflow.com/questions/20519040/search-in-all-files-in-a-project-in-sublime-text-3).

# Rename the current file

When you are editing a file, you may wish to change its name. First you should
[reveal it in the side bar](# Show current file in side bar). Then right click
on the file and rename it.

# Quick goto a class or a function

By pressing <kbd>Ctrl</kbd>+<kbd>R</kbd>, we can navigate to functions/class in
the current file. This ability is really powerful and it works for multiple
source file types such as `\*.py`, `\*.md`, `\*.cpp`.

# Edit a variable simultaneously

Sometimes, we want to rename a variable name, but it have several occurrences in
the source code. It is easy to select all occurrences of a variable in Sublime
Text. You should first point the cursor to the variable and then hit
<kbd>Alt</kbd>+<kbd>F3</kbd>. Now you can edit the variable simultaneously.

# Quickly go to a line

Press <kbd>Ctrl</kbd>+<kbd>G</kbd> and enter the line number, then press
<kbd>Enter</kbd>, you will go to that line.

# Unindent lines

You have two choices. The first is to use <kbd>Shift</kbd>+<kbd>Tab</kbd>,
which works only for multiple selected lines. In order to make it work for a
single line, set

```yaml
"shift_tab_unindent": true,
```
in your user settings.

You can also use <kbd>Ctrl</kbd>+<kbd>[</kbd> to un-indent lines on Windows and
Linux (<kbd>Ctrl</kbd>+<kbd>]</kbd> is used to indent lines).

# Stop hidden files and folders from showing in the sidebar

By default, hidden file and folders (files and folders starting with a dot) are
shown in the sidebar. According to [this Stackoverflow
post](https://stackoverflow.com/questions/17632108/hide-files-with-certain-extension-in-sublime-text-editor),
to disable this behaviour, you need to edit the User settings (just click
`Preferences -> Settings`): add the following options to the User setting file:

```yaml
"file_exclude_patterns":
    [
        "*.pyc", "*.pyo", "*.exe", "*.dll", "*.obj","*.o","*.a", "*.lib", "*.so", "*.dylib", "*.ncb", "*.sdf", "*.suo", "*.pdb", "*.idb", ".DS_Store",
        "*.class", "*.psd", "*.db", "*.sublime-workspace", ".*"
    ],
"folder_exclude_patterns":
    [
        ".svn", ".git", ".hg", "CVS", ".*"
    ]
```

The pattern `.*` matches any file or folder which starts with a dot. The above
options works effectively to prevent the hidden files and folders from showing
up in your sidebar. You can also add your own custom patterns to the two
exclude lists.

# References

+ [Disable previewing file when right click on it](https://github.com/SublimeTextIssues/Core/issues/2).
+ [Search a file in current folder](https://stackoverflow.com/a/17613588/6064933).
+ [Open and close side bar and more](https://stackoverflow.com/questions/15178358/how-to-use-sidebar-with-the-keyboard-in-sublime-text-2-and-3).
+ [Documentation on search a string in current project](http://docs.sublimetext.info/en/latest/search_and_replace/search_and_replace_files.html#search-filters).
