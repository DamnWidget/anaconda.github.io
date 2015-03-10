---
date: 2014-10-21T20:03:59+01:00
draft: true
title: A Powerful IDE
---

Anaconda re-implement some Sublime Text 3 features and implements another ones
on it's own.

# Python Auto Completion

Anaconda uses the powerful [jedi](https://github.com/davidhalter/jedi) library
to offer advanced auto-completion capabilities to the end user.

## How it works?

When the user is writing, eventually, Sublime Text 3 shows a popup with possible
code completions based on the open files buffers, if `tab` (if auto completion
on tab is enabled in Sublime Text 3) or `ctrl+space` is pressed, the user can
force the shown of this popup.

Anaconda hooks the regular Sublime Text 3 call and add it's own completion
results asking about available completions to the (included in the plugin) jedi
library that return a list of the possible words to complete and which type
of object is (class, function, parameter, import etc).

This process is totally transparent for the user as it happens in totally
asynchronous way so the user is never aware that the auto-completion engine
is running under the hood.

When anaconda has a result, it sends it back to the Sublime Text 3 that will
shown the popup that we already spoke to the user that can then select whatever
word she want to use.

### AutoCompletion configuration

The auto-completion will work out of the box (if your configured Python
interpeter is valid and is in the PATH) but it can be tunned with several
options.

#### Autocompletion on dot [.]

If the user want to trigger the autocompletion when she write down the dot
character it can be configured easily editing the `Python.sublime-settings`
configuration file in the  `Packages/User` directory (Preferences -> Browse
Packages) and add the following:

```json
{
    "auto_complete_triggers": [{"selector": "source.python - string - comment
    - constant.numeric", "characters": "."}]
}
```

**note**: it's possible that the user have to create this file if it doesn't
exists yet

#### Remove Python snippets from completions

The user can also choose to don't show Python snippets in their autocompletion
results setting the option `hide_snippets_on_completion` as `true` in the
plugin or project configuration.

#### Complete function and class parameters

If the option `complete_parameters` is set as `true`, anaconda will add class
and method/function parameters to its completions when you type `(` after a
completion has been done.

If `complete_all_parameters` is set as `true`, it will add all the possible
parameters, is it's set as `false`, only mandatory parameters will be placed.

No key binding is needed to use this feature so it doesn't interfere in any
way with your Sublime Text 3 normal operations.<br><br><br>

# Goto Definition

Sublime Text 3 already implement `goto` functions but it requires that you
have the file opened in one of your active buffers. Anaconda is able to go to
a file where a variable, function, method, class or module is defined whatever
is located on (always that is not a built-in symbol).

## How it works?

Anaconda just asks the underlying jedi engine where the symbol under the
cursor is defined and jumps there.

### How to use it?

This feature can be triggered in several ways:

* Linux: `super+g`
* OS X and Windows: `ctrl+alt+g`
* Vintage Mode (Command Mode): `gd`
* Context Menu: `Anaconda > Goto Definition`
* Command Palette: `Command Palette > Anaconda: Goto`
<br><br><br>

# Find Usages

With this command, the user can find all the locations where a symbol (
variable, function, method, class or module) is being used.

## How it works?

Again, anaconda asks the underlying jedi engine where the symbol under the
cursor is being used and present a list of possible locations, if the user
select one, the file listed is opened (if it wasn't already, and the cursor
is located on it's line). An white blinking gutter arrow appears for some
seconds to indicate the user where the cursor is located at.

### How to use it?

The find usages command can be triggered in the following ways:

* Linux: `super+f`
* OS X and Windows: `ctrl+alt+f`
* Context Menu: `Anaconda > Find Usages`
* Command Palette : `Command Palette > Anaconda: Find`
<br><br><br>

# Display Signatures

## How it works?

When enabled, anaconda will show information about the methods that the
programmer are typing into their Sublime Text 3 in the status bar. If
the version of Sublime Text 3 in use is build 3070 or superior and the
anaconda's settings `enable_signatures_tooltip` and `merge_signatures_and_doc`
are set as `true`, anaconda will display a tooltip with useful information
about the method or class being used.

![](/anaconda/img/tooltips.png)

# Get Documentation

Anaconda can look for and show the user the docstring of whatever function,
method, class, module or package. The user just have to put the cursor over the
symbol that want to get the docstring from (or after a parenthesis, for example
after write `sys.exit(`) and then trigger the command to get the function
signature and docstring in a bottom panel without lose focus form the buffer.

## How it works?

As usual, anaconda asks the underlying jedi engine about the symbol under the
cursor signature and docstring (if any) and then show it to the user using
an additional panel or a tooltip.

### How to use it?

The command can be triggered with:

* Linux: `super+d`
* OS X and Windows: `ctrl+alt+d`
* Context Menu: `Anaconda > Show Documentation`
* Command Palette: `Command Palatte > Anaconda: Show`

# Refactor Rename

With this command, the user can (try to) rename the object under the cursor
in a project basis scope in a safe way.

### How to use it?

The command can be trigger using the context menu `Anaconda > Rename object
under the cursor`<br><br><br>

# McCabe code complxity checker

The users can run the
[McCabe complexity checker](http://en.wikipedia.org/wiki/Cyclomatic_complexity)
tool in whatever python file that they want. It's threshold can be adjusted
configuring the option `mccabe_threshold` in the configuration file on in
the project configuration file.

## How it works?

Anaconda includes part of the [McCabe](https://github.com/flintwork/mccabe)
Python tool and can execute it in any open buffer, get the results and present
a list of functions (and lines) that presents a complexity higher than the
configured threshold. If no complexity is found, a descriptive message
appears in the status bar.

### How to use it?

The McCabe complexity checker can be fired:

* Using the Context Menu: `Anaconda > McCabe complexity check`
* Using the Command Palette: `Command Palette > Anaconda: McCabe`
<br><br><br>

# Autoimport undefined names

Anaconda can add an `import <undefined_name>` literal at the end of the imports
block if the anaconda auto importer is used in an undefined name in the buffer.

**note**: Anaconda will **NOT** check if that import is valid or not before
place it in the buffer.

## How it works?

Anaconda auto-import feature is really a dummy or toy feature and is under
redesign to offer a better experience to the user.

### How to use it?

Just place the cursor over the undefined name in the buffer and use the
context menu `Anaconda > Autoimport undefined word under the cursor`
<br><br><br>

# Imports Validator

One of the latest additions to anaconda is the ability to validate the imports
in the project files if the `validate_imports` option is set as `true`. Note
that this option is set as `false` by default.

## How it works?

Anaconda asks the underlying jedi engine about the buffer/file/project imports
to detect if the symbols are valid and the configured python interpreter is
able to see them.

**Note**: Anaconda does not execute the imports or any other code related with
them

**Note 2**: Anaconda is not able to detect as valid imports that depends on
dynamic stuff like addition of specific paths to the `sys.paths` list, if
the users project uses them, they have to take into account that anaconda
is going to mark those imports as non valid even if they are valid in runtime.
For this reason, the users can add the `# noqa` magic comment on those ones
to tell anaconda to don't mark them as invalid.

(Yes, it will validate imports made with the auto import feature described
above)<br><br><br>

# Anaconda's Build System

As you already know, one of the weakness of Sublime Text 3 is that it uses
it's embedded Python interpreter when you try to build the files that you
are working on.

Anaconda adds it's own build system that is based in your system Python
interpreter instead (or in any python interpreter that you configure in
your `python_interpreter` option).

The name of the anaconda's python builder is `Anaconda Python Builder` and
you should be able to find it under your Sublime Text's `Tools` menu as soon
as you install anaconda in your Sublime Text if you are using projects.

**note**: if you change your `python_interpreter` in your configuration,
anaconda will rewrite your project file to update the python interpreter
used by the build system automatically.

**note**: If you want to know more about `python_interpreter` in anaconda,
take a look at [Configure Anaconda the Right Way](/anaconda/anaconda_settings/)
<br><br>

# Anaconda Linting

Anaconda was based/inspired/ported from the old Sublime Text 2 SublimeLinter
plugin. Although anaconda linter was inspired by SublimeLinter, anaconda
linting is much faster than SublimeLinter (for ST3) and SublimeLinter3 for
several reasons:

1.- Anaconda does not use delayed queues to perform the linting work, instead
of that, anaconda fire a single call to the linter methods `n` seconds after
the last key was pressed by the user while typing. Those `n` seconds can be
configured by the user (by default is `0.5s`)
2 .- Anaconda is totally asynchronous so it never blocks the Sublime Text GUI,
because that, anaconda's linting is smooth and flawless.

**note**: SublimeLinter 3 improved considerably their linting trigger
mechanisms, they can still block the ST3 GUI but seems to be smoother than
old Sublime Linter for Sublime Text 2

## How it works?

Anaconda listen for events that come from the Sublime Text GUI itself, in
certain events, the linter can be fired, for example, after save or open a
buffer. When the linting process is fired, anaconda send a request to the
Anaconda's JsonServer (a standalone and isolated server where all the heavy
processment takes place) to lint the related buffer and return back the
control to Sublime Text 3 immediately, when the Anaconda's JsonServer has
linted the buffer, it return it back to anaconda that proceed to lint the
buffer. Anaconda linting is really smooth and fast.

As anaconda can use whatever python interpeter that the user want to use (
including virtual environments and remote interpreters), anaconda can lint
the code for a Python version different than the version included with
Sublime Text 3 (Python 3.3.3).

## Linting behaviour

* **Always mode (defaut)**: When `anaconda_linting_behaviour` is set as
`always`, the linters are fired in the background as the user edit the
file that is working on and in load/save events. The linting process is fired
also when a buffer gains the application focus. It is performed in the
background in an external application and is handled in another execution
thread so it doesn't block the Sublime Text GUI ever. The process is fired
when the plugin detects that the user stopped typing for a determined and
configurable period of time that can be defined setting the value of the
configuration variable `anaconda_linter_delay`, that is `0.5s` default.
* **Load and Save mode**: When `anaconda_linting_bahaviour` is set as
`load-save`, the linters are fired on load/save and focus gain only.
* **Save only mode**: When `anaconda_linting_behaviour` is set as `save-only`
the linters are fired on file saving only.

## What can anaconda lint?

Anaconda can lint:

* Syntax errors and inconsistencies (using PyFlakes or PyLint)
* PEP8 Violations
* PEP257 Violations

PyFlakes, pep8 and pep257 libraries are included in the plugin, to use PyLint
instead of PyFlakes, the PyLint utility has to be installed and be visible
by the user's configured python interpreter.

**note**: PEP257 linter is disabled by default
**note**: PyLint can't lint unsaved buffers

## Disabling the linter

There is people that doesn't care about linting or they just use another
plugin to do it, they can completely deactivate this feature setting the
`anaconda_linting` as `false` in the anaconda or project configuration.

**note**: imports validation depends of anaconda linting handler so it will
not work if anaconda linting is disabled

## Disabling the linter in certain files

Sometimes, the users need to open or work on files that they don't maintain
at all because they are part of a third party software, deprecated code or
they have been written by developers that doesn't care about the PEP8. In
those situations the users can completely disable the linting in just those
files using the Command Palette `Anaconda: Disable linting
on this file`.

Disabled files will persist between Sublime Text 3 sessions, and they can
be linted again using the Command Palette `Anaconda: Enable linting on this
file`.

## Enabling pep257

Anaconda supports docstrings linting using [pep257](http://legacy.python.org/dev/peps/pep-0257/)
specification. This feature is disabled by default but can be enabled setting
the `pep257` as `true` in the configuration file or the project file.

## Disabling certain errors for pep257

Specific errors can be disabled adding them (as string elements into a list) on
the `pep257_ignore` user settings in the config file. The `D209` is disabled
by default as it has been deprecated.

## Disabling pep8

Pep8 violations can be disabled setting the value of the `pep8` configuration
variable as `false`.

## Disabling certain pep8 errors

Is also possible to disable just some errors like the infamous `line too long
E501` error in pep8. It can be done adding them to the `pep8_ignore` list using
the error code like:

```json
"pep8_ignore":
[
    "E501"
]
```

There is an equivalent for PyFlakes errors called `pyflakes_ignore`, look at
the section below for details.

## Disabling specific PyFlakes errors

The user can also disable specific PyFlakes errors (unused import module for
example) uncommenting them in the `pyflakes_explicit_ignore` list in the
global anaconda configuration file or adding this list to any project
configuration with the warning/errors that they wish to disable. For example,
to disable the unused import warning:

```json
"pyflakes_explicit_ignore":
    [
        "UnusedImport"
    ],
```

**note**: for specific information about the different mechanisms that can
be used to configure anaconda, refer to the [Configuring Anaconda](https://damnwidget.github.com/anaconda/configuration) section.

## Using PyLint as PyFlakes alternative

Anaconda has full support for PyLint as linter application but some
considerations has to be taken before do it.

Due 3rd party dependencies required for PyLint, Anaconda doesn't add it like do
with pep8 and PyFlakes libraries, if users want to use PyLint as their linter
they need to download and install it by themselves.

Anaconda does not use a subprocess to call the PyLint linter like Pylinter
plugin does. We just import some files from pylint and run the inter from the
JsonServer process capturing the system stdout file descriptor. That means
that anaconda *will* use your configured python interpreter (and environment)
in order to lint your files with PyLint so it should be installed in your
virtual environment if you are using virtualenv.

PyLint *doesn't* support lint buffers that are not saved yet in the file
system so it *can't* lint files until you save them.

Anaconda uses E, W and V codes to maintain compability with PyFlakes and PEP8
linters so the PyLint mapping is as follows:

```json
mapping = {
    'C': 'V',
    'E': 'E',
    'F': 'E',
    'I': 'V',
    'R': 'W',
    'W': 'W'
}
```

PyLint errors can be ignored using the configuration option `pyling_ignore`.
When PyLint is used, PyFlakes is totally turned off.

**note**: PyLint can be really annoying use it at your own risk

## Showing linting error list

Users can show a quick panel with all the errors in the file that they are
working on using the command palette `Anaconda: Show error list` or in the
contextual menu `Anaconda > Show error list`

## Jump to the next error

Users can use the `Anaconda: Next lint error` command from the Command Palette
or from the context menu to navigate trough the lint errors on the file. This
is useful for anyone fixing PEP8 violations in a file for example.
<br><br><br>

# Autoformat PEP8 Errors

Anaconda supports the [AutoPEP8](https://github.com/hhatto/autopep8)
tool and its integrated and distributed as part of the plugin itself. Users
can reformat their files to follow PEP8 automatically using the Command
Palette `Anaconda: Autoformat PEP8 Errors` or choosing the same option in the
contextual menu.

The autoformat operation is done asynchronous but as it's a heavy process, it
can timeout before has any effect, take a look in the next sections to know
a bit more about autoformat and it's options.

## How it works?

AutoPEP8 is not the fastest tool in the world as it perform several complex
syntactic checks and parses. When the autoformat is fired, anaconda run it
into a separate process and continue it's normal operations but the buffer
that the user asked to autoformat is set as `read-only` while the autoformat
operation returns back the new modified buffer or a timeout/error occurs.

## What about the timeout?

The timeout is one second long by default and can be configured setting the
configuration option `auto_formatting_timeout` in your project settings or
in the global anaconda configuration settings file.

## Fire PEP8 autoformating on save

The autoformating process can be fired automatically when the file is saved
setting the configuration option `auto_formatting` as `true` in the options.

## Supported PEP8 Autoformat errors list?

Autoformat can handle the following list of errors:

> E101 - Reindent all lines.<br>
E111 - Reindent all lines.<br>
E121 - Fix indentation to be a multiple of four.<br>
E122 - Add absent indentation for hanging indentation.<br>
E123 - Align closing bracket to match opening bracket.<br>
E124 - Align closing bracket to match visual indentation.<br>
E125 - Indent to distinguish line from next logical line.<br>
E126 - Fix over-indented hanging indentation.<br>
E127 - Fix visual indentation.<br>
E128 - Fix visual indentation.<br>
E129 - Indent to distinguish line from next logical line.<br>
E201 - Remove extraneous whitespace.<br>
E202 - Remove extraneous whitespace.<br>
E203 - Remove extraneous whitespace.<br>
E211 - Remove extraneous whitespace.<br>
E221 - Fix extraneous whitespace around keywords.<br>
E222 - Fix extraneous whitespace around keywords.<br>
E223 - Fix extraneous whitespace around keywords.<br>
E224 - Remove extraneous whitespace around operator.<br>
E225 - Fix missing whitespace around operator.<br>
E226 - Fix missing whitespace around operator.<br>
E227 - Fix missing whitespace around operator.<br>
E228 - Fix missing whitespace around operator.<br>
E231 - Add missing whitespace.<br>
E241 - Fix extraneous whitespace around keywords.<br>
E242 - Remove extraneous whitespace around operator.<br>
E251 - Remove whitespace around parameter '=' sign.<br>
E261 - Fix spacing after comment hash.<br>
E262 - Fix spacing after comment hash.<br>
E271 - Fix extraneous whitespace around keywords.<br>
E272 - Fix extraneous whitespace around keywords.<br>
E273 - Fix extraneous whitespace around keywords.<br>
E274 - Fix extraneous whitespace around keywords.<br>
E301 - Add missing blank line.<br>
E302 - Add missing 2 blank lines.<br>
E303 - Remove extra blank lines.<br>
E304 - Remove blank line following function decorator.<br>
E401 - Put imports on separate lines.<br>
E501 - Try to make lines fit within --max-line-length characters.<br>
E502 - Remove extraneous escape of newline.<br>
E701 - Put colon-separated compound statement on separate lines.<br>
E702 - Put semicolon-separated compound statement on separate lines.<br>
E703 - Put semicolon-separated compound statement on separate lines.<br>
E711 - Fix comparison with None.<br>
E712 - Fix comparison with boolean.<br>
W191 - Reindent all lines.<br>
W291 - Remove trailing whitespace.<br>
W293 - Remove trailing whitespace on blank line.<br>
W391 - Remove trailing blank lines.<br>
E26  - Format block comments.<br>
W6   - Fix various deprecated code (via lib2to3).<br>
W602 - Fix deprecated form of raising exception.<br>

<br><br>

# Look & Feel

Anaconda look & feel can be extensively customized.

## Gutter Marks

Gutter marks are enabled by default, they can be disabled setting the
configuration option `anaconda_gutter_marks` as `false` in the plugin or
project configuration files.

By default, the `basic` gutter marks theme is used, this is, round marks
are displayed in the left gutter bar of the Sublime Text 3 buffer. To display
fancy icons, the users can set the `anaconda_gutter_theme` configuration
option to any of the available themes:

Gutter Theme | Error | Warning | Violation
------------ | ----- | ------- | ---------
Alpha        | ![Error](/anaconda/img/gutter_themes/alpha-illegal.png) | ![Warning](/anaconda/img/gutter_themes/alpha-warning.png) | ![Violation](/anaconda/img/gutter_themes/alpha-violation.png)
Bright       | ![Error](/anaconda/img/gutter_themes/bright-illegal.png) | ![Warning](/anaconda/img/gutter_themes/bright-warning.png) | ![Violation](/anaconda/img/gutter_themes/bright-violation.png)
Dark        | ![Error](/anaconda/img/gutter_themes/dark-illegal.png) | ![Warning](/anaconda/img/gutter_themes/dark-warning.png) | ![Violation](/anaconda/img/gutter_themes/dark-violation.png)
Simple        | ![Error](/anaconda/img/gutter_themes/simple-illegal.png) | ![Warning](/anaconda/img/gutter_themes/simple-warning.png) | ![Violation](/anaconda/img/gutter_themes/simple-violation.png)

## Error lint style

Anaconda will draw boxes around the errors, warnings, and violations in the
code that is being linted, this behaviour can be configured setting the value
of the configuration option `anaconda_linter_mark_style`, the possible options
are:

* outline (the defautlt) - it will draw a border line around the affected lines
* fill - it will draw a border and fill it with background color around the
affected lines
* none - it will not draw anything

Outline                                          | Fill
------------------------------------------------ | ----
![outline](/anaconda/img/mark_style/outline.png) | ![fill](/anaconda/img/mark_style/fill.png)

## Error underlines

The characteristic red underline that appears under the errors on the lines,
you can set the `anaconda_linter_underlines` as `false`.

**note**: that option takes effect only when the `anaconda_linter_mark_style`
is set to `none`.

## Persist linter marks while typing

Anaconda will remove all the lint marks from the buffer while the user is
typing by default, this behaviour can be also configured setting the option
`anaconda_linter_persistent` as `true`.

## Linting theme customization

Users can customize anaconda linting marks as they like adding some custom
rules to their SublimeText theme:

Tag name | Suggested background (dark) | Suggested foregorund (dark)
-------- | --------------------------- | ---------------------------
anaconda.outline.illegal | #ff4a52 | #ffffff
anaconda.underline.illegal | #ff0000 | N/A
anaconda.outline.warning | #df9400 | #ffffff
anaconda.underline.warning | #ff0000 | N/A
anaconda.outline.violation | #ffffff33 | #ffffff
anaconda.underline.violation | #ff0000 | N/A

### XML File example

You can of course copy this piece of XML and paste into your theme

```xml
<!-- Anaconda -->
<dict>
  <key>name</key>
  <string>anaconda Error Outline</string>
  <key>scope</key>
  <string>anaconda.outline.illegal</string>
  <key>settings</key>
  <dict>
      <key>background</key>
      <string>#FF4A52</string>
      <key>foreground</key>
      <string>#FFFFFF</string>
  </dict>
</dict>
<dict>
  <key>name</key>
  <string>anaconda Error Underline</string>
  <key>scope</key>
  <string>anaconda.underline.illegal</string>
  <key>settings</key>
  <dict>
      <key>background</key>
      <string>#FF0000</string>
  </dict>
</dict>
<dict>
  <key>name</key>
  <string>anaconda Warning Outline</string>
  <key>scope</key>
  <string>anaconda.outline.warning</string>
  <key>settings</key>
  <dict>
      <key>background</key>
      <string>#DF9400</string>
      <key>foreground</key>
      <string>#FFFFFF</string>
  </dict>
</dict>
<dict>
  <key>name</key>
  <string>anaconda Warning Underline</string>
  <key>scope</key>
  <string>anaconda.underline.warning</string>
  <key>settings</key>
  <dict>
      <key>background</key>
      <string>#FF0000</string>
  </dict>
</dict>
<dict>
  <key>name</key>
  <string>anaconda Violation Outline</string>
  <key>scope</key>
  <string>anaconda.outline.violation</string>
  <key>settings</key>
  <dict>
      <key>background</key>
      <string>#ffffff33</string>
      <key>foreground</key>
      <string>#FFFFFF</string>
  </dict>
</dict>
<dict>
  <key>name</key>
  <string>anaconda Violation Underline</string>
  <key>scope</key>
  <string>anaconda.underline.violation</string>
  <key>settings</key>
  <dict>
      <key>background</key>
      <string>#FF0000</string>
  </dict>
</dict>
```
