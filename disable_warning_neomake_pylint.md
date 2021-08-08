---
title: "Use Pylint for Code Checking inside Neovim"
date: 2018-09-20 17:31:00 +0800
tags: [pylint]
categories: [Nvim]
---

Pylint is a popular static code checker which can effectively find the possible
bugs in your source code. It is easy to install pylint with conda or pip:

<!--more-->

```
conda install pylint
# pip install pylint
```

# Pylint configuration

The pylint configuration file is named `.pylintrc` which controls its
behaviors. Pylint will try to read the configuration from default locations.
The locations of the configuration file vary from system to system:

+ Linux/Mac: `~/.pylintrc`
+ Windows: `%userprofile%\.pylintrc` (`%userprofile%` is a system variable. You
can use `echo %userprofile%` to show its value.)

You can disable certain warnings and errors in the configuration file, which
will apply globally for every Python source file you check.

# Usage

## pylint warning “module has no member"

For Python packages which are partly written in C/CPP, such as Numpy and
PyTorch, pylint may not be able to find the relevant method for an object. You
can set up in `[TYPECHECK]` section of `.pylintrc` to disable warning about
unfound method for Numpy and PyTorch:

```
[TYPECHECK]

# List of members which are set dynamically and missed by pylint inference
# system, and so shouldn't trigger E1101 when accessed. Python regular
# expressions are accepted.
generated-members=numpy.*,torch.*
```

## Disable warning for a certain line

If you only want to disable a certain warning or error for a specific line. You
can add `#pylint: disable=some-message,another-message` in the end of the line.
For example:

```python
sys.path.insert(0, '..')
import mymodule  #pylint: disable=import-error
```

You can also use the warning or error code instead of the actual message:

```python
sys.path.insert(0, '..')
import mymodule  #pylint: disable=E0401
```

## Disable warning for a certain file

If you want to disable a certain warning or error for a particular file, you
can add `#pylint: disable=some-message` at the top of the file.

## Find the msg id of a certain message ##

To disable a certain warning, we want to know its message id. On the command
line, use the following command:

```bash
pylint --list-msgs |grep message
```

For example, to find the message id of `unnecessary-comprehension`, we can use:

```bash
pylint --list-msgs|grep unnecessary-comprehension
```

We may also find the message id from [pylint doc](http://pylint.pycqa.org/en/latest/technical_reference/features.html#).

# Configure and use pylint with Neovim

To use pylint inside Neovim, you need to install a linting plugin.
[Neomake](https://github.com/neomake/neomake) is a good choice. The minimum
configuration for Neomake is:

```
" when to activate neomake
call neomake#configure#automake('nrw', 50)

" which linter to enable for Python source file linting
let g:neomake_python_enabled_makers = ['pylint']
```

Neomake will automatically run pylint for currently edited file and show
warning and error symbols in sign column. You can also use `:lopen` to open the
location list window to see all the warnings and errors. To navigate the
location list, use `:lprevious` and `:lnext` to go to the previous and next
item on the list respectively.

## How to ignore warnings in Neomake

When we lint our Python code with Neomake (using
[Pylint](https://github.com/PyCQA/pylint)), it is often the case that our code
can not pass the checker 100%. A lot of warnings and errors may occur, e.g., no
docstring for method, variable names not conforming to standard. For some
warnings, we do not want them to occur. How to ignore these warnings?

For example, if we want to ignore
[`invalid-name`](http://pylint-messages.wikidot.com/messages:c0103)(corresponding
code is `C0103`) and
[`missing-docstring`](http://pylint-messages.wikidot.com/messages:c0111)（
corresponding code `C0111`），we can use two methods.

### Configure in the nvim config file

The first way is to configure through Nvim configuration file. Add the
following settings to your Nvim configuration:

```vim
let g:neomake_python_pylint_maker = {
  \ 'args': [
  \ '-d', 'C0103, C0111',
  \ '-f', 'text',
  \ '--msg-template="{path}:{line}:{column}:{C}: [{symbol}] {msg}"',
  \ '-r', 'n'
  \ ],
  \ 'errorformat':
  \ '%A%f:%l:%c:%t: %m,' .
  \ '%A%f:%l: %m,' .
  \ '%A%f:(%l): %m,' .
  \ '%-Z%p^%.%#,' .
  \ '%-G%.%#',
  \ }

let g:neomake_python_enabled_makers = ['flake8', 'pylint']
```

Find the code corresponding to a warning and paste it in the string after `-d`
options. Separate different warning codes with `,`.

### Configure in `.pylintrc`

The second way is to create a `.pylintrc` file and ignore certain warning in
this file. First, we need to generate `.pylintrc` under the home directory:

```
pylint --disable=invalid-namae,missing-docstring --generate-rcfile > ~/.pylintrc
```

If you want to ignore new warnings, you just need to edit `.pylintrc`, find the
part starting with `disable=` and add the new warnings to the long list of
existing warnings. The format should conform to the existing format in the
`.pylintrc` file.

# References

+ <https://github.com/neomake/neomake/issues/612>
+ <https://stackoverflow.com/questions/22448731/how-do-i-create-a-pylintrc-file/52362063#52362063>
+ <http://pylint.pycqa.org/en/latest/faq.html#how-do-i-find-the-option-name-for-pylintrc-corresponding-to-a-specific-command-line-option>
+ https://github.com/pytorch/pytorch/issues/701
+ https://stackoverflow.com/questions/20553551/how-do-i-get-pylint-to-recognize-numpy-members
+ https://stackoverflow.com/questions/28829236/is-it-possible-to-ignore-one-single-specific-line-with-pylint
+ http://pylint.pycqa.org/en/latest/faq.html
+ <http://pylint-messages.wikidot.com/all-messages>
