---
date: 2016-08-30T12:26:09+01:00
draft: true
title: Using a Remote Python Interpreter
---

# Remote Python Interpreters

Anaconda can use remote python interpreters to lint and complete your code. Some IDE utilities will not work when remote python interpreters are in use, for example the Goto IDE command will not work if you try to go to a file that is stored in the remote hosts and not in the local one.

## Remote machine preparation

In order to use remote python interpreters living on remote machines with anaconda the user has to prepare the remote machine first following the next steps.

## Clone anaconda source code in the remote host

You need the anaconda source code in your remote host (if you don't have it there yet) to do so, just connect trough SSH or remote desktop into your remote host and clone the anaconda repository in whatever directory that you like:
```bash
git clone http://github.com/DamnWidget/anaconda
```

## Start the anaconda `minserver.py`

The regular anaconda JsonServer just don't work in remote environments for that, anaconda also distributes a minified version of the JsonServer called `minserver.py` you must execute it with the Python interpreter that you want to use to lint and complete your code, the following are the `minserver.py` options:

```
Usage: minserver.py -p <project> -e <extra_paths> port
```
Let's say that you cloned anaconda source code in `~/ides/anaconda` and you want to use a Python interpreter in a virtual environment called `django_prj1` you could start your server like:

```bash
$ workon django_prj1
$ (django_prj1) python ~/ides/anaconda/anaconda_server/minserver.py &
```
This command will start anaconda's `minserver.py` in the background. Note that if you close your ssh session the server would finish as well, if you want to be able to close your session you should be using something like [screen](http://www.gnu.org/software/screen/), [tmux](http://tmux.github.io/) or [nohup](http://linux.die.net/man/1/nohup)

## Making anaconda to connect to your remote host

Anaconda uses the `python_interpreter` setting value to connect to your remote host using the URI `tcp://<host>:<port>` so simple use something like this as your `python_interpreter` configuration option:

```json
"python_interpreter": "tcp://192.168.100.21:9999"
```

*Note*: Take into account that the `minserver.py` *must* be running before you start Sublime Text 3 or set anaconda to use your remote hosts and your remote host:port will be available from your network or anaconda will fail (and complain about).

