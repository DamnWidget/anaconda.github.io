---
date: 2016-08-30T12:22:53+01:00
draft: true
title: Docker Integration
---

# Docker Containers

Anaconda can use docker containers environments to lint and complete your code. Some IDE utilities will not work or don't offer its full features when docker environments are in use, for example, the Goto IDE command wil not work if you try to go to a file that is located in the container (workarounds are provided anyway).

## How to run the anaconda's minserver into a Docker container?

There are so many ways to make your anaconda to connect and use a minserver running in a Docker container. The way to use Docker with anaconda is to use docker run, docker exec or docker-compose manually to start your application environment and then use a regular anaconda's remote worker using the generic `tcp://address:port` configuration with whatever directory map that you want or need (remember that directory maps is a common feature for all the anaconda's remote workers so it is present in `tcp://` and `vagrant://` python interpreter schemes).

We are gonna present here different ways to connect your anaconda with Docker, some of them make use of `docker run`, others use `docker exec` in an already running container (that probably contains your code) and others doesn't directly use the `docker` command but `docker-compose` with a `docker-compose.yml` file.

### Run anaconda's minserver in it's own container

If you just need to use the Python interpreter installed in the container you can just run a new container that executes the anaconda's minserver with the desired interpreter and set the `python_interpreter` to point with a tcp remote connection to your container.

#### Run the container
For this example we are  gonna use the generic `python:2.7` docker image but it will work for whatever docker image that contains a valid Python installation. The command to run our container will look like this:
```bash
docker run -d --rm -v ~/.config/sublime_text_3/Packages/Anaconda:/opt/anaconda -v ~/my_project:/my_project python:2.7 /opt/anaconda/anaconda_server/docker/start python 19360 docker_project
```

The `-d` option will make docker to run the container in detach mode in the background and return its ID, the `--rm` option will automatically remove the container as soon as it exit. With the `-v` option we pass the directory where Anaconda is installed inside Sublime Text 3 (for Linux in this example) as a volume to be mounted inside the container in `/opt/anaconda` in that way, the anaconda `minserver` code will be available in the container to be executed so we don't need to make a new installation into the container and we can be sure that it is always up to date with the last release. We also pass the directory where our code resides `~/my_project` as a volume to be mounted in `/my_project` using another `-v` parameter (you can mount as many volumes as you need passing each one in a new `-v` parameter).

Then the last parameter is the command that we want to run `/opt/anaconda/anaconda_server/docker/start` with the parameters `python`, `19360` and `docker_project`, `docker/start` is a shell script wrapper that executes the anaconda's `minserver` using it's first argument as python interpreter and passing it's second as port and third as project name, there is a fourth argument that we didnt' used here to specify `extra_paths` separated by comma.

#### Anaconda `python_interpreter`

With our container running the only thing that we have to do is to tell anaconda that we want to use a remote tcp interpreter:

```json
{
    "settings": {
        "python_interpreter": "tcp://172.17.0.2:9999?pathmap=~/my_project,/my_project"
    }
}
```

Et voila our anaconda will use our container.

**note**: the address `172.17.0.2` is the address that docker assigns automatically to it's first container in it's by default `bridge` network, using it we don't have the need to expose the minserver port, if we want to expose the port to the host we should run the docker command as
```bash
docker run -d --rm -v ~/.config/sublime_text_3/Packages/Anaconda:/opt/anaconda -v ~/my_project:/my_project -p 9999:9999 python:2.7 /opt/anaconda/anaconda_server/docker/start python 19360 docker_project
```

Then we could use `"tcp://localhost:9999...."` as our intrepreter

### Run anaconda's minserver in an already running container

In many situation we will have already a container executing our code so we can use it to run the `minserver` using the `exec` docker command.

```bash
docker exec -d dd94c34814f5 /opt/anaconda/anaconda_server/docker/start python 19360 some_project
```

The `-d` option tells docker to run this command in detached mode, the second parameter `dd94c34814f5` is our container ID and the third and last parameter is our command. Take into account that `exec` will not be able to mount volumes so the anaconda code **must** be already a volume in the container that we are executing the command.

#### The `python_interpreter`

Exactly the same but with a different port

```json
{
    "settings": {
        "python_interpreter": "tcp://172.17.0.2:19360?pathmap=~/my_project,/my_project"
    }
}
```

**note**: take into account that `exec` is not able to expose new ports to the host neither so if you want to expose the port where the anaconda's `minserver` is running you should add it first to the `run` command on the container that we are executing the command.

### Using docker-compose

We think this one is the best approach to follow, if you are not used to the `docker-compose` command take look at it's [documentation](https://docs.docker.com/compose/) in the docker website. In short, `docker-compose` allow us to define and run multi container isolated environments with docker.

We are gonna borrow their [getting started](https://docs.docker.com/compose/gettingstarted/) tutorial so go there and follow the instructions until you finalize the step 3. If you followed the tutorial you will have a directory that contains four files: `app.py`, `docker-compose.yml`, `Dockerfile` and `requirements.txt` now edit `docker-compose.yml` file, we are gonna add a new container there.

Add the new container to the `services` definition after the redis entry with the contents below (be careful with the spaces as them have meaning in the YAML format):

```yaml
    anaconda:
        image: composetest_web
        ports:
            - "19360:19360"
        volumes:
            - /home/<user>/.config/sublime_text_3/Packages/Anaconda:/opt/anaconda
        depends_on:
            - web
        entrypoint: /opt/anaconda/anaconda_server/docker/start python 19360 docker_project /code
```

As we used `composetest_web` as our base image, we should have the `/code` volume available so we pass it as fourth parameter to our `minserver` invocation so Jedi will be able to complete code in our application.

Now you can go forward the step four of the `docker-compose` getting started guide and run `docker-compose up` to start the environment.

**note**: change the left side of the volume path to whatever path your anaconda is installed on.

**example**: you can take a look at this [gist complete example](https://gist.github.com/DamnWidget/3e58cec118879ed7baa08dc283e162a0)

#### The `python_interpreter`

The python interpreter is not more difficult

```json
{
    "settings": {
        "python_interpreter": "tcp://localhost:19360?pathmap=<path_to_your_docker-compose.yml_directory>,/code"
    }
}
```

Enjoy!
