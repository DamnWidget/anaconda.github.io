---
date: 2014-10-24T13:45:09+01:00
draft: true
title: Anaconda's Test Runner
---

The anaconda's test runner is an original contribution by [@NorthisUp](https://github.com/NorthIsUp)
to the project.

# How to run tests?

There is different ways to access the tests runner commands in Anaconda, the
most common one is just click with the secondary mouse click in the file that
you want to run tests from and select the option that you want from the
anaconda's contextual menu.

You can also use the Command Palette `Anaconda: test` if you don't want to
remove your hand from the keyboard.

**note**: Of course you can configure whatever shortcut that you want to run
anaconda tests but they are not added by default

## Available options

* **Run Last Tests**: It will repeat the last test that you ran
* **Run Current Test**: It will run the test under the cursor
* **Run Project Tests**: Run the project whole test suite
* **Run Current File Tests**: Run all the tests defined in the current file

## Configuration Options

Anaconda's test runner support several options to fine tune it's behaviour,
to run some kind of test runners (like twisted's trial) you need to configure
them in order to make it work

**note**: By default, anaconda's test runner try to run `nosetests`

### test_before_command

If this options is set, anaconda will try to run the given command before run
the test suite.

**note**: If you need to run more than one command, just use a list of
commands like:

```json
"test_before_command": ["cmd1", "cmd2", "cmd3"]
```

### test_after_command

As before, if this option is set, anaconda will try to run the given command
after run the test suite, this is useful to clean up

**note**: again you can pass a list of commands with the same format than for
the `test_before_command` option

### test_command

This is the command that anaconda is going to run in order to execute your
test suite, this is `nosetests` by default. An example of configuration is
as follows:

```json
"test_command": "python -m unittest discover"
```

### test_delimeter

This option defines the test delimiter to use after your test module names,
by default this is `:`, for example, if you set this option to `.` and your
module nae is `"test_server.py"` it will try `test_server.ServerTest`

### test_virtualenv

Unfortunately, anaconda's test runner can't use your defined
`python_interpreter`, this is why you have to configure this option to tell
the tests runner that you want to use an specific virtual environment to run
your test suite.

Anaconda will activate the virtual environment, run the suite and deactivate it.

### test_project_path

If this option is set, anaconda will add whatever text is on it after the
command that is going to be used to run the test suite, this is needed for
example to run test suites that uses the `twisted` library `trial` command.

For example, if we have this configuration:

```json
"test_command": "trial",
"test_project_path": "mamba"
```

Anaconda will execute `trial mamba` (where `mamba` is a directory)
<br><br>

# Configuration Examples

You can find here several configuration examples. All the configuration
options on these examples are placed in the `.sublime-project` file.

**note**: If you don't know what a `.sublime-project` file is and why is
so important, take a look at [Configuring Anaconda the Right Way](/anaconda/anaconda_settings/)

## Run Twisted's Trial Suite

Normally, to run a test suite build with Twisted's trial you need to pass to
trial a top level directory that contains your tests and is used by it's auto
test discovery facility. Here is an example about how to configure the
anaconda's tests runner to accomplish that.

```json
{
    ...
    "setings": {
        "test_command": "trial",
        "test_delimeter": ".",  // trial uses "." as delimeter
        "test_project_path": "myprojct"
    }
}
```

The configuration above will make anaconda to run `trial` in `myproject`
directory

## Run Twisted's Trial using before and after commands for virtualenv

You don't need to use the `test_virtualenv` option at all if you don't want
to do it. This is useful for example when you have complex steps before and
after activate or deactivate your virtual environment.

```json
{
    ...
    "settings": {
        "test_command": "trial",
        "test_delimeter": ".".  // trial uses "." as delimeter
        "test_project_path": "myproject",
        "test_before_command": "source $HOME/.virtualenvs/myproject/bin/python",
        "test_after_command": "deactivate"
    }
}
```

## Run Twisted's Trial using test_virtualenv

Obviously, is you just want to use a virtual environment is much easier to
just use the `test_virtualenv` option, this example es equivalent to the
previous one.

```json
{
    ...
    "settings": {
        "test_command": "trial",
        "test_delimeter": ".".  // trial uses "." as delimeter
        "test_project_path": "myproject",
        "test_virtualenv": "~/.virtualenv/myproject"
    }
}
```

## Run Django test suite wit nose2

To run a Django project test suite with nose2 is not much more complex

```json
{
    ...
    "settings": {
        "test_command": "./manage.py test --settings=tests.settings --noinput",
        "test_delimeter": "."
    }
}
```

## Common python code and standard library unittest

Of course, you can also run the standard library unittest suite

```json
{
    ...
    "settings": {
        "test_command": "python -m unittest discover"
    }
}
```
