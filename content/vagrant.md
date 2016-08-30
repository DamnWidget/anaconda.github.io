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
only in the vagrant box (as Sublime Text 3 will not be able to find it
to open it).

## Vagrant guest machine preparation

In order to use remote python interpreters living on guest vagrant machines
with anaconda, the user has to prepare the guest environment first following
the next steps.

### Add local anaconda installation as shared folder

Your guest machine needs access to the anaconda code base to run a minified version of the `jsonserver` that is already present in your anaconda installation. To add your local anaconda installation as shared folder just add the configuration below in your `Vagrantfile`
```ruby
{config}.vm.synced_folder '{local_anaconda_installation}', '{mount_point}'
```
Change `{config}` for the name of the config of your vm box (if your machine is the default machine it will be just `config.vm.synced_folder` but if for example your machine is called `django_project1` it will be `django_project1.vm.synced_folder`).

Change `{local_anaconda_installation}` to the path where anaconda is installed in your system, that will be:

- Linux: `~/.config/sublime-text-3/Packages/Anaconda`
- OS X: `~/Library/Application Support/Sublime Text 3/Packages/Anaconda`
- Windows: `%APPDATA%\\Roaming\\Sublime Text 3\\Packages\\Anaconda`

Change `{mount_point}` to whatever directory inside your guest machine that you want to mount it, for example `/anaconda`

So for example to share the anaconda local installation directory using OS X as host and the default config in `Vagrantfile` and using the vagrant home as mount point you should use something like:

```ruby
config.vm.synced_folder '~/Library/Application Support/Sublime Text 3/Packages/Anaconda' '~/anaconda'
```

### Configure Sublime Text 3 project

To tell anaconda to use your vagrant environment instead of your local one we just need to change our `python_interpreter` setting, anaconda uses a non standard URI format to configure Vagrant environments, the formats is as follows:

```ruby
vagrant://{machine_name}:{port}?{options}
```

So for example, if we want to use our vagrant guest in a forwarded port we could use the URI:
```json
"python_interpreter": "vagrant://default:19360?network=forwarded"
```

The above `python_interpreter` will try to start an anaconda's `json minserver` using the by default Python interpreter in the guest machine (python) on the by default anaconda's guest machine shared folder (/anaconda) and connect to `localhost:19360` where it's port should be forwarded.

The different configuration options that you can pass to it are:

| Option | Meaning |
| --- | --- |
| network | Network topology to use, it could be one of forwarded, private or public |
| address | Used to specify the address that anaconda should connect, this is used in conjunction with `network=private` |
| dev | Used to specify which network interface to use when using public topologies (`network=public`) |
| shared | Used to specify the location of the anaconda code in the guest machine |
| interpreter | Used to specify the path to the Python interpreter in the guest machine |
| extra | Used to specify extra folders to add to the code paths in the guest machine |

As this maybe a bit hard to understand you can find ready to use `Vagrantfiles` and `python_environments` in the next section to help the reader to understand how it works, it is really easy.

### Ready to use examples

This section contains ready to use examples, you can use them or modify them to adapt to your own specific needs.

#### Local Forwarded Port using Defaults

Forwarded ports allow you to access a port on your host machine and have all data forwarded to a port on the guest machine, over either TCP or UDP. Configure one is as easy as set the right configuration in your `Vagranfile`.

Anaconda expects to find the anaconda plugin directory in the guest machine in `/anaconda` for POSIX systems or `C:\\anaconda` for Windows (this location can be configured), it also uses the default `python` interpreter if no interpreter is provided. This is the aspect of a valid vagrant file to work in that default scenario (obviously, the vagrant guest must have a Python interpreter installed and available in the `vagrant` user path):

##### Vagrantfile
```ruby
Vagrant.configure(2) do |config|
    config.vm.box = "precise64"
    config.vm.network :forwarded_port, guest: 19360, host: 19360
    config.vm.synced_folder = '~/Library/Application Support/Sublime Text 3/Packages/Anaconda', '/anaconda'
end
```
##### Anaconda's Python Interpreter
```json
"python_interpreter": "vagrant://default:19360?network=forwarded"
```

#### Private Network using Defaults

Private networks allow you to access your guest machine by some address that is not publicly accessible from the global internet. You can configure a private network for your guest machine using setting up the `vm.network` option in your `Vagrantfile`, for example:

##### Vagrantfile
```ruby
Vagrant.configure(2) do |config|
    config.vm.box = "precise64"
    config.vm.network "private_network", ip: "192.168.5.10"
    config.vm.synced_folder = '~/Library/Application Support/Sublime Text 3/Packages/Anaconda', '/anaconda'
end
```
##### Anaconda's Python Interpreter
```json
"python_interpreter": "vagrant://default:19360?network=private&address=192.168.5.10"
```
We tell anaconda to connect to the address `192.168.5.10` using the query option `address`

#### Public Network using Defaults

Public networks allow your guest machines to be allocated from others hosts in your local network (or event from the internet). To configure a public network just set the `vm.network` config option in your `Vagrantfile` like:

##### Vagrantfile

```ruby
Vagrant.configure(2) do |config|
    config.vm.box = "precise64"
    config.vm.network "pubic_network"
    config.vm.synced_folder = '~/Library/Application Support/Sublime Text 3/Packages/Anaconda', '/anaconda'
end
```
##### Anaconda's Python Interpreter

```json
"python_interpreter": "vagrant://default:19360?network=public&dev=eth1"
```
When the vagrant network mode is set as public, anaconda will discover the guest IP address through the vagrant ssh command in the local machine this is why we have to specify the guest interface device that is gonna set it's IP (`eth1` in this example).

#### Use a custom Python interpreter

If we want to use an specific Python interpreter in the guest machine (this is the equivalent to set the `python_interpreter` to some specific interpreter in a local configuration) we can just setup the `interpreter` query option.

```json
"python_interpreter": "vagrant://default:19360?network=forwarded&interpreter=~/.virtualenv/myenv/bin/python"
```
Anaconda will try to execute the Python interpreter located in `~/.virtualenv/myenv/bin/python` in the guest machine.

#### Add extra paths

You can add extra paths just adding them to the `extra_paths` configuration like in your regular anaconda configuration or you can pass them in your URI, you can also mix both, for example:

```json
"python_interpreter": "vagrant://default:19360?network=forwarded&extra=~/my_firs_project&extra=~/my_second_project"
```
You can add multiple extra paths defining more than one `extra` query option as is shown in the example

**Note**: The extra path **must** exists in the target host not in the host that ST3 is running.

#### Specify another shared folder mount point

To specify another mount point for the anaconda code, we have to modify both `Vagrantfile` and `python_interpreter`:

##### Vagrantfile

```ruby
    config.vm.synced_folder = '~/Library/Application Support/Sublime Text 3/Packages/Anaconda', '/home/vagrant/anaconda'
```
##### Anaconda's Python Interpreter

```json
"python_interpreter": "vagrant://default:19360?network=forwarded&shared=/home/vagrant/anaconda"
```

#### Using an user different than vagrant
In some situations the user could need to use an specific user in the guest machine, that could be easily done adding the user and password to the network location part in the URI so for example to use the user `maya` with password `12345` we could simple add:

```json
"python_interpreter": "vagrant://maya:12345@default:19360?network=forwarded"
```

### Mapping directories

Some of the anaconda's IDE features can't simple work when using vagrant, docker or a remote host that runs the `minserver.py` because even if the environments share directories and code (common case), the paths are different and the `minsever.py` is not able to find files to parse or the `Sublime Text 3` can't find files to jump to.

A simple example of this problem is as follows, imagine that we configured our vagrant environment to use a forwarded port and share the local or host directory `/home/user/projects/myproject` as `/MyProject` in the vagrant guest machine. Then we write a class `A` in `/home/user/projects/myproject/class_a.py` and a class `B` in `/home/user/projects/myproject/class_b.py` that import `A`, if we try to use `Doc`, `Signature Tooltips` or `Goto` to any symbol defined in `A` from `B`, what ST3 is gonna send to the `minserver.py` is something like: Execute command `<command>` in handler `jedi` with parameters:
* code: <file_source_code>
* line_number: 10
* column: 23
* filename: /home/user/projects/myprojects/class_b.py
* charset: utf8

Obviously, the file path is wrong as that path simply doesn't exists in the vagrant guest.

To minimize (completely solve it is just not possible) this problem effects, you can provide a series of (local, remote) paths to make anaconda try to map always that is possible paths between local and remote files. So for example to fix our scenario above we can use the following vagrant URI:
```json
"vagrant://default:19360?network=forwarded&pathmap=/home/user/projects/myproject,/MyProject"
```
Anaconda will try to map the paths always that is possible (and safe), you can add multiple paths adding the `pathmap` query parameter more than once.

### Advanced Users

Anaconda will try to start it's minimified JsonServer in your guest environment using `vagrant ssh` command for you, if you are a vagrant advanced user or you need to use some specific configuration or simply you want to have control yourself of the `minserver.py` script and how it is executed you can totally bypass the anaconda process startup and it will just try to connect to the guest but ignoring any startup preparation.

In order to bypass anaconda startups you just need to pass the `manual` option with whatever value that you want

```json
"python_interpreter": "vagrant://default:19360?manual=1"
```
Using the config above anaconda will just try to connect to `localhost:19360`.

You can also bypass anaconda startup options using a regular anaconda remote worker instead of a vagrant one:

```json
"python_interpreter" "tcp://localhost:19360"
```

*Note*: Take into account that in both scenarios you *must* start the `minserver.py` manually before to start Sublime Text 3 or anaconda will fail (and complain about).



