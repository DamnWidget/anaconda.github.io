---
date: 2014-10-22T21:20:53+01:00
draft: true
title: Configure Anaconda the Right Way
---

Anaconda works fine out of the box (always that there is a Python interpreter
configured in your path and the binary is named `python`) but if you want
to get the max from it, you can configure multitude of options to tune it
and adapt it to your needs.

To take a look at the common configuration of the Anaconda powerful IDE
features, take a look at the <a href="anaconda/IDE/">Powerful IDE</a> section,
in this section we will speak about where and how to configure anaconda to use
multiple **virtual environments**, create hook config files and other anaconda
specific options that can make your life way easier.

Let's start from the beginning.

# Does Anaconda supports virtualenv?

Yes, it does. Anaconda supports virtual environments out of the box, but you
have to tell it where to find the python binary of your virtual environment.

This can be done configuring the `python_interpreter` option in any of the
several ways that anaconda allow you to fine tune your plugin installation.

# Where to place Anaconda settings options?

You can place anaconda settings in three different places.

\# | Description           | File Name                      | File Location
---- | --------------------- | ------------------------------ | -------------
1 | Global Settings       | Anaconda.sublime-settings      | `Packages/Anaconda/Anaconda.sublime-settings`
2 | User Settings         | Anaconda.sublime-settings      | `Packages/User/Anaconda.sublime-settings`
3 | Project Configuration | \<project>.sublime-project | Your project location...

If you choose the first and second options, take into account that you are
effectively configuring Anaconda globally so the plugin will behave in the way
that you configure it for any Python file that you edit if there is no a
specific project configuration file that hides and override your global
configurations (more about this later).

To configure the plugin using the global or user anaconda settings file, just
go to `Preferences > Package Settings > Anaconda`, there you will find two
different entries, `Settings-Default` and `Settings-User` for options 1 and 2
respectively. Any option in those files have to be placed in the global scope.

```json
{
    "python_interpreter": "stackless_python3",
    "auto_python_builder_enabled": false,
    ...
}
```

**note**: `Settings-Default` should be pre-populated with all the available
anaconda plugin options configured to it's default behaviour.

**note**: We **strongly recommend** to use Project configurations always<br><br>

## Overriding details per project

Anaconda will always try to use any options that are found in the project file
of the project that you are working on first. This is pretty useful as **you
can override and modify the behaviour of the plugin depending of your current
environment**.

For example, if you are working in an **Open Source** project that follows
full PEP8 and strict coding standards, you can configure your anaconda linters
in totally strict mode but relax them in other environments where such
practices are (unfortunately) not being used.

To archive this desirable behaviour you can add whatever configuration option
that you need in your project configuration file, including (and specially)
the `python_interpreter` one.

To edit your project configuration file just go to `Project > Edit Project`.
The options **must be placed inside the `settings` dictionary**

```json
{
    "build_systems":
    [
        {
            "name": "Anaconda Python Builder",
            "selector": "source.python",
            "shell_cmd": "/home/damnwidget/.virtualenvs/anaconda/bin/python -u \"$file\""
        }
    ],
    "folders":
    [
        {
            "follow_symlinks": true,
            "path": "."
        }
    ],
    "settings":
    {
        "python_interpreter": "/home/damnwidget/.virtualenvs/anaconda/bin/python"
        ...
    }
}
```

# Python Interpreter settings

Anaconda has the ability to use whatever python interpreter that you can
compile and use in your platform, this includes:

Python Interpreter | Python 2x | Python 3x
------------------ | --------- | ---------
**CPython**        | ![](/anaconda/img/check.png) | ![](/anaconda/img/check.png)
**PyPy**           | ![](/anaconda/img/check.png) | ![](/anaconda/img/check.png)
**Jython**         | ![](/anaconda/img/check.png) | N/A
**Stackless**      | ![](/anaconda/img/check.png) | ![](/anaconda/img/check.png)

Anaconda allows you to complete, linting, analyze and use all the anaconda
features with whatever python interpreter that you want, that of course
includes **python interpreters residing in virtual environments**

## Configuring the Python Interpreter

Anaconda will use your `PATH` configured python interpreter by default out
of the box. To use another interpreter just change the `python_interpreter`
configuration option globally or (more usually) in your project files as is
shown in the examples below

Example of global user configuration (Packages/User/Anaconda.sublime-settings):
```json
{
    "python_interpreter": "/usr/bin/pypy-c2.4"
}
```

Exaple of project configuration (~/projects/my_project/MyProject.sublime-project):
```json
{
    "folders":
    [
        {
            "follow_symlinks": true,
            "path": "."
        }
    ]
    "python_interpreter": "~/virtualenvs/my_project/bin/python"
}
```

## Extra Paths

I know, you are used to add loads of extra paths to make your completions
work in other plugins. In Anaconda, `extra` means just that `extra`, your
Anaconda plugin will be able to lint, complete and analyze any package and
module that your configured python interpreter is able to see.

That means that you should use this options **only for real extra packages**
that are not in your `site-packages` (SublimeText python files in the
sublime text installation directory for example). You can add as many extra
paths as you need in a list separated by commas in both your global or
project configuration files.

Example of global user configuration (Packages/User/Anaconda.sublime-settings):
```json
{
    "extra_paths":
    [
        "/opt/sublime_text_3",
        "/opt/maya/SundayPipeline2014/SundayPython"
    ]
}
```

Exaple of project configuration (~/projects/my_project/MyProject.sublime-project):
```json
{
    ...
    "settings": {
        "extra_paths":
        [
            "/opt/sublime_text_3",
            "/opt/maya/SundayPipeline2014/SundayPython"
        ]
    }
}
```

## Virtualenv environment variables

If you are using a `virtualenv` for your `python_interpreter` and you start
your Sublime Text 3 from the command line (to inherit environment variables
on OS X and Linux) you can use the variable `$VIRTUAL_ENV` in your
`python_interpreter` configuration option.

Example of global user configuration (Packages/User/Anaconda.sublime-settings):
```json
{
    "python_interpreter": "$VIRTUAL_ENV/bin/python"
}
```

Exaple of project configuration (~/projects/my_project/MyProject.sublime-project):
```json
{
    ...
    "settings": {
        "python_interpreter": "$VIRTUAL_ENV/bin/python"
    }
}
```

## Environment hook files

There are high probabilities that the only configuration option that you
ever change in your projects is just the `python_interpreter` and the
`extra_paths`. If that is your case, maybe you want to use environment hook
files instead of configure your projects directly.

### What is an environment hook file?

An environment hook file is a JSON file named `.anaconda` that resides in the
root of your working directory or in any directory level up to drive root
directory. If a valid hook file exists on that directory tree, it will be used
instead of your project or general anaconda configuration. A valid `.anaconda`
hook file is as in the example below.

```json
{
    "python_interpreter": "~/.virtualenvs/stackless_python2.7/bin/python",
    "extra_paths": ["/usr/local/lib/awesome_python_lib"]
}
```

**note**: take into account that only `python_interpreter` and `extra_paths`
can be hooked, any other option will be ignored

## Project and Python Interpreter Switching

If you change your configured python interpreter or you just switch your
project, the plugin will detect it and reload a new completion/linting/IDE
JsonServer killing the old one in a total transparent way so you don't need
to restart your Sublime Text 3.

**note**: is for some strange reason you don't want that your anaconda plugin
behave in this way, you can always change the configuration option
`auto_project_switch` to `false` and anaconda **will not** auto switch the
interpreter as described above.

**warning**: Windows users can experiment weird behaviour after a project or
python interpreter switch due inconsistence in the WinSocket state, if this
happens, Anaconda will complain and show an error window. The only solution
if this happens is restart Sublime Text 3.

**note**: The plan is to port Tulip from Python 3.4 into anaconda to replace
the anaconda's custom asynchronous IOLoop.

