---
title: "Learning Expect Programming"
date: 2019-10-29 01:42:59+0800
tags: [Expect, Regex]
categories: [technique]
---

In [my previous post](https://jdhao.github.io/2018/10/08/server_autologin_with_expect/),
I talked about how to automate the server login process with the help of Expect
programming language. This post continues my learning process.

<!--more-->

# Difference between `send` and `send_user` command?

There are two similar commands in Expect, `send` and `send_user`. `send` and
`send_user` have different purposes. `send` is used to provide input for the
spawned processes, while `send_user` is used to send some message to via
terminal to the users.

# Difference between `\n` and `\r` in Expect?

According to my test, when you use `send` to send characters to the spawned
process, it makes no difference to use either `\r` (carriage return) or `\n`
(line feed). They both works as if the user has press the <kbd>Enter</kbd> key.

However, they work differently when you want use `send_user` to display
something to the user. `\n` will give a new line while `\r` will just move the
cursor to the beginning of the line (i.e., your message will be erased). To
verify, execute the following command

```expect
expect {
    "prompt1" {send_user "answer1\n"}
    "prompt2" {send_user "answer2\r"}
}
```

If you execute the above script with expect, when you input `prompt1`, you will
get the output `answer1` and a new line is started; when you input `prompt2`,
you get nothing in the screen. Actually, `answer2` is shown and is then erased
by `\r`.

# Check if a file exists

Sometimes, we may want to check if a file exists and take some actions based on
the condition. We use [`file exists
<name>`](http://tcl.tk/man/tcl8.5/TclCmd/file.htm#M15) to check that:

```expect
if {[file exists .ssh/known_hosts]} {
    spawn rm .ssh/known_hosts
}
```

# Match optional prompts

Sometimes, there are optional prompts which may occur depending on some
conditions. For example, when we first log into a new server, we are warned
that the authenticity of the server can not be established and if we want to
continue connecting. After that, we may not see this prompt anymore. We can use
the `exp_continue` command to achieve that.

```expect
expect {
    "Are you sure*" {
        send "yes\r"
        exp_continue
    }
    "*password: " {
        send "pass\r"
    }
}
```

The `exp_continue` command will continue the expect block until it matches
another pattern or timeouts.

# Pattern matching

## Double quotes and curly braces in pattern matching

In expect script, both double quotes and curly braces can be used to group
argument. They are quite different.

Inside double quotes, variable substitution and command substitution and
backslash substitution take place:

+ variable substitution: `$var` will be replaced with actual value of variable `var`.
+ command substitution: `[some_char]` is considered as command invoking, and
he result of executing command `some_char` replaces `[some_char]`. Also see
[this guide page.](https://www.tcl.tk/man/tcl8.5/tutorial/Tcl5.html)
+ backslash substitution: characters after ` \ ` is replaced with the character
verbatim. There are several exceptions to this rule. For example, `\n`, `\t`,
`\uHHHH` etc. have special meanings. For a complete list, see [this
page](https://wiki.tcl-lang.org/page/Dodekalogue) (the part about backslash
substitution).

Inside double braces, command substitution, variable expansion and backslash
substitution does not take place.  For example:

```
expect {
    # match $var literally
    {$var} {send_user "yes\n"}
    # match character \ and $
    {\$} {send_user "yes\n"}
    # match character \ and n
    {\n} {send_user "yes\n"}
}
```

## Glob match patterns

For simple matching, glob patterns can be used, and it works similarly to the
bash glob functionality. `*` is used to represent any number of characters, and
`?` is used to represent a single character. For more info about glob patterns,
see [doc here](https://www.tcl.tk/man/tcl8.5/tutorial/Tcl16a.html).

Some patterns and their meanings are listed below:

```
expect{
    # match strings containing abc
    expect *abc*
    # match literal [a]
    {\[a]} {send_user "yes\n"}
    # match literal [a]
    {\[a\]} {send_user "yes\n"}
    # match strings containing ab followed by another character
    {ab?} {send_user "yes\n"}
    # match `abc` or `abd`
    {ab[cd]} {send_user "yes\n"}
}
```

## Regex matching

If you want to match more complex patterns, you can also use the [regex
provided by the Tcl
language](http://www.tcl.tk/man/tcl8.5/TclCmd/re_syntax.htm#M66)[^1]. To use
regex, you will use the `-re` option for `expect` command. For example, to
match either `ab` or `cd`, you can use the following command:

```
expect -re ab|cd {send_user "hello\n"}
```

As discussed before, since patterns inside the double quotes undergo several
substitutions, you should take extra care when you want to match patterns using
regex. For example, to match consecutive digits, you should use `expect -re
"\\d+"` or `expect -re {\d+}`.  If you use `expect -re "\d+"`, you will match
consecutive  character`d` since backslash will translate `\d` inside double
quotes.

```
 expect {
    # the following two patterns both match two consecutive alphabet chars.
    -re "\[a-z\]{2}" {send_user "yes\n"}
    -re {[a-z]{2}} {send_user "yes\n"}
    # match a lenth 2 string which contains only alpha-numerical chars.
    -re {^[a-z0-9]{2}\n$} {send_user "yes\n"}
    timeout {send_user "time out\n"}
 }
```

## Matching bracket literally

One extremely complex and bewildering  pattern is to match square bracket
literally. It is difficult because square bracket have special meanings in both
command substitution and in regex or glob pattern.

If you use double quotes, in order to match `[a]` literally, you have to use
triple backslash to escape it:

```
expect -re "\\\[a\\\]" {send_user "yes\n"}
# or you can use
expect "\\\[a\\\]" {send_user "yes\n"}
```

First backslash is to prevent command substitution. Second backslash and third
backslash is to prevent backslash substitution so that regex engine can see one
backslash.  So the final pattern regex engine sees is actually `\[a\]`, which
prevents a from being interpreted as a regex range. Thus, it is matched
literally. [This page](https://www.tcl.tk/man/tcl8.5/tutorial/Tcl21.html) also
explains why three backslashes are needed  in order to match literal square
bracket if we use double quotes.

To prevent such complex patterns, it is highly encouraged to use double braces
to simplify the pattern. The above pattern can be simplified as the following:

```expect
expect {
    {\[a\]} {send_user "yes\n"}
}
```

# References

+ [Expect check if file exists.](https://stackoverflow.com/questions/23909157/how-to-find-if-a-file-exists-in-expect-script)
+ [Expect: match optional prompt.](https://stackoverflow.com/a/52893825/6064933)
+ [Difference between `\r` and `\n` in expect.](https://stackoverflow.com/a/26187389/6064933)
+ [Expect regex](https://stackoverflow.com/questions/17064555/using-regular-expressions-with-expect)
+ [Expect glob and other patterns](https://www.oreilly.com/library/view/exploring-expect/9781565920903/ch04.html)
+ [Tcl regex syntax references.](http://www.tcl.tk/man/tcl8.5/TclCmd/re_syntax.htm#M66)
+ [Grouping with double quotes.](https://www.tcl.tk/man/tcl8.5/tutorial/Tcl3.html)

[^1]: expect language is an extension to the Tcl programming language.
