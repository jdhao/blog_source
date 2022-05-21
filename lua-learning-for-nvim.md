---
title: "Lua Learning Notes"
date: 2022-05-21T11:11:54+08:00
markup: markdown
tags: [Lua]
categories: [Programming]
---

As a long time nvim user, I am learning Lua and slowly transition my nvim config to lua.
In this script, I will share some tips and lessons I have learned the hard way.

<!--more-->

## The colon operator

In Lua code, we may see the colon operator followed by some object method.
For example, if we have a string `x` and want to find the length of this string.
In Python, we do `x.len()`, but this will be an error in Lua:

<style type="text/css">
@import url('//maxcdn.bootstrapcdn.com/font-awesome/4.2.0/css/font-awesome.min.css');

.error-msg {
    color: #D8000C;
    background-color: #FFBABA;
    margin: 5px 0;
    margin-bottom: 20px;
    padding: 10px;
    border-radius: 5px 5px 5px 5px;
    border: 2px solid transparent;
    border-color: transparent;
}
</style>

<div class="error-msg">
    <i class="fa fa-times-circle"> Error</i></br>

    stdin:1: bad argument #1 to 'len' (string expected, got no value)
    stack traceback:
        [C]: in function 'string.len'
        stdin:1: in main chunk
        [C]: in ?
</div>

In Lua, we need to use `x:len()` (or you can use `string.len(x)` like `len(x)` in Python):

```lua
x = 'foo bar'
x.len()  --> this is an error
x:len()  --> works without errors
```

`x` is of type `string`, when we use `x.len()`, if it does not exist, lua will look at the metatable of x and find the `__index` key.
The metatable of `x` is like this:

```
{
  __index = {
    byte = <function 1>,
    char = <function 2>,
    dump = <function 3>,
    find = <function 4>,
    format = <function 5>,
    gmatch = <function 6>,
    gsub = <function 7>,
    len = <function 8>,
    lower = <function 9>,
    match = <function 10>,
    rep = <function 11>,
    reverse = <function 12>,
    sub = <function 13>,
    upper = <function 14>
  }
}
```

It will then execute the function corresponding to key `len`.
However, since function `len()` requires an argument, so we get the above error.

If we instead use `x:len()`, we are implicitly pass x itself as the first argument:

> A call v:name(args) is syntactic sugar for v.name(v,args), except that v is evaluated only once.

So we are actually using `x.len(x)`. In fact, if we use `x.len(x)`, it works perfectly fine.

Ref:

+ Lua has no class but dot operator?: https://stackoverflow.com/q/44522241/6064933
+ Lua: When is it possible to use colon syntax?: https://stackoverflow.com/q/33052241/6064933
+ https://docs.coronalabs.com/tutorial/basics/colonDotOperators/index.html

## The difference between defining function with colon and dot

As discussed in the above section, when we use colon operator in Lua,
we are implicitly passing the object itself (called `self`) to the function.

Suppose we want to implement a string function to concatenate two strings, we can use both the dot and colon operators.
But the function signature will be a little different:

```lua
local x = "hello world"

function string.add1(str1, str2)
  print(string.format("str1: %s, str2: %s", str1, str2))

  return string.format("%s%s", str1, str2)
end

function string:add2(s)
  print(string.format("str1: %s, str2: %s", self, s))

  return string.format("%s%s", self, s)
end


print(x:add1("abc"))
print(x:add2("abc"))
```

In this example, if we use dot operator, we must explicitly provide the two parameters.
For the colon operator, the first string is implicitly provided as `self`, which you can access in the function body.
So this function has only one parameter.

If you run the code, their result will be exactly the same.

Ref:

+ Lua: colon notation, 'self' and function definition vs. call: https://stackoverflow.com/q/32080972/6064933

## The weirdness of lua REPL

If we have the following code in `test.lua`:

```lua
local x = 1
print(x)
```

it will print out `1`, not `nil`. Expected result, nothing special.

In the lua REPL, if we type the following command:

```lua
> local x = 1
> print(x)
```

the output is surprisingly `nil`, not 1! Crazy, isn't it?

The reason is that in lua REPL, each line is treated as a block.
Basically, a block is an area where a variable is visible.
Local variables only work in that block. For example, the following works:

```lua
> local x = 1; print(x)
```


This is because when lua execute a file, it treat the file as a block,
so local variable `x` does not expire in the next line.
Actually we can manually create a block in lua file with `do ... end` (see doc [here](https://www.lua.org/pil/4.2.html)).

It is a little weird, but that is how lua REPL works.

Ref:

+ https://stackoverflow.com/a/33155461/6064933
+ https://stackoverflow.com/a/2835433/6064933

## In lua, both 0 and empty string is true

Unlike Python, both 0 and empty string is considered `true` in lua, which is weird.

```lua
if '' then
  print('str is not empty')
else
  print('str is empty')
end

if 0 then
  print('zero is true')
else
  print('zero is false')
end
```

If you run the above code, you will get the following output:

```
str is not empty
zero is true
```

## Lua table index starts from 1, not 0

This feature trips me really hard and I ended up debugging my code for nearly half of an hour.
So if we have a table `local a = {1, 2, 3}`, the first element will be `a[1]` not `a[0]`.

I have repeated forgotten this and made mistakes.

Ref:

+ https://stackoverflow.com/q/2785704/6064933
+ http://www.cs.utexas.edu/~EWD/ewd08xx/EWD831.PDF

## Why use parentheses around literal strings when call its method?

If you use a literal string and want to call string method, you need to wrap the string with parentheses.
This is how the Lua syntax works.

+ Right: `print(("hello"):len())`
+ Wrong: `print("hello":len())`

Ref:

+ https://stackoverflow.com/q/48143667/6064933
