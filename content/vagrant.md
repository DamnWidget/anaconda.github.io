---
date: 2014-10-24T14:40:35+01:00
draft: true
title: Vagrant Integration
---

Anaconda supports five basic vagrant commands that can be used through the
Command Palette:

* Vagrant Init
* Vagrant Up
* Vagrant Reload
* Vagrant Status
* Vagrant SSH

To be able to use those commands you have to add a minimum valid configuration
file to use vagrant integration in your project.

# Vagrant Environments

Anaconda knows how to use vagrant boxes environments to lint and complete
your code. Some IDE utilities could not work (depending on the feature and
how is your project hierarchy) when vagrant environments are in use. For
example, the Goto IDE command will not work for third party libraries if
you don't have those libraries installed in your development machine but
only in the vagrant box (as Sublime Text 3 will not be able to found it
to open it).

## Vagrant guest machine preparation

In order to use remote python interpreters living on guest vagrant machines
with anaconda, the user has to prepare the guest environment first following
the next steps.

### Clone anaconda in the guest machine

Your guest machine is going to use a modified version of `anaconda_lib` and
anaconda's `json_server` which you can just checkout from the anaconda git
repository.

```bash
(vagrant guest machine) $ git clone https://github.com/DamnWidget/anaconda
(vagrant guest machine) $ cd anaconda
(vagrant guest machine) $ git checkout vagrant_server
```

The `vagrant_server` branch is maintained separately from the main project
but it should implement the same features.

**note**: you are responsible of update your `vagrant_server` branch when
there is an anaconda update, you can do it just doing a `git pull` in the
directory inside your vagrant guest.

### Configure your vagrant_server

Now tat you have a fresh copy of the vagrant server, the next step is to
edit the configuration file to adapt it to your needs.

There is three different ways to configure the vagrant support depending
of what your specific needs are.

The `vagrant_server` configuration file is just a python script that defines
the value or four variables

```python
python_interpeter = "python3"       # the python interpreter to use
project = "MyAmazingProject"        # the name of the project (for logging
purposes mainly )
extra_paths = None                  # homologous to regular anaconda
extra_paths (they must live in the guest machine)
port = '19360'                      # the port to listen on (must be a string)
```

### Starting the vagrant_server

Next step is to start the server executing the file `server.py` with any
python interpreter that you want.

```bash
(vagrant guest machine) $ python server.py
```

### Configure Sublime Text 3 project

Next step is to configure your Sublime Text 3 project to use the new vagrant
environment.

**note**: vagrant environments works `per-project` only, that means there is
no a global anaconda vagrant configuration .Anaconda will try to use always a
local python interpreter if not specific project configuration is provided so
the user will always provide a valid project configuration file in order to use
vagrant support with anaconda.

A vagrant configured anaconda project looks like this

```json
{
    "build_systems":
    [
        {
            "name": "Anaconda Python Builder",
            "selector": "source.python",
            "shell_cmd": "python -u \"$file\""
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
        "vagrant_environment": {
            "directory": "~/vagrant",       // Directory where the Vagrantfile is located in your local machine
            "machine": "Ubuntu-14.04",      // If no machine is provided, default will be used
            "network": {
                "mode": "private",          // configured vagrant network mode
                "address": "192.168.5.10",  // guest machine IP address
                "port": 19360               // guest machine anaconda vagrant_server port
            }
        }
    }
}
```

**note**: anaconda can't switch from regular to vagrant environment so you
have to restart your Sublime Text 3 after this step
<br><br>

# Vagrant Network Configuration

Vagrant support works only when vagrant network is properly configured, the
possible valid configurations are shown below

## Forwarded Port

Forwarded ports allow you to access a port on your host machine and have all
data forwarded to a port on the guest machine, over either TCP or UDP.

Configure one is as easy as set the right configuration in your `Vagrantfile`.

```ruby
    config.vm.network :forwarded_port, guest: 19360, host: 1936
```

Then you can configure your anaconda to use this port in your local host to
connect to the guest machine jsonserver.

```json
    ...
    "network": {
        "mode": "forwarded",
        "port": 1936
    }
```

## Private Networks

Private networks allow you to access your guest machine by some address that
is not publicly accessible fro the global Internet. You can configure a private
network for your guest machine setting up the `vm.network` option in your
`Vagrantfile` like

```ruby
    config.vm.network "private_network", ip: "192.168.5.10"
```

You have to specify a vagrant network configuration in your anaconda project
configuration, for a private network with the previous settings it will be

```json
    ...
    "network": {
        "mode": "private",
        "address": "192.168.5.10",
        "port": 19360
    }
```

## Public Networks

Public networks allow your guest machines to be allocated from other hosts in
your local network (or even from the Internet). To configure a public network
just set the `vm.network` config option in your `Vagrantfile` like

```ruby
    config.vm.network :public_network
```

In this case, we don't have to specify the address of our guest machine in
the anaconda configuration (mainly because we don't know it)

```json
When the vagrant network mode is set as public, anaconda will discover the
guest IP address trough the `vagrant ssh` command in automatic way in the
local machine.

```
