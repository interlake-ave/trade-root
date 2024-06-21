# Preface

This guide assumes you are using a UNIX based system. Windows is not
recommended for developing with docker, you should at minimum be using
WSL (Windows Subsystem Linux) and have some familiarity with basic
command line.

Docker images all run on a linux hypervisor to ensure consistent
behavior across different platforms. More information about
the docker daemon can be found [here](https://docs.docker.com/guides/docker-overview/)

# dev-template

This is meant to be a template to help set up your dev env.
Using docker-compose in production is not recommended, but helps
test and verify your entire (or partial) stack locally, increasing agility
and speed of development.

When deploying an application it is recommended to use kubernetes
or define deployment parameters inside a deployment script.

# Setup Instructions

Clone this repoistory to your local, and then delete the .git folder.
This is meant to be a template to help guide setup in order to develop
a full stack locally.

# Directory setup

Your project directories should follow this structure in order to use `docker-compose`
```
├── project-root/
│   ├── app-frontend/
│   │   ├── Dockerfile
│   │   ├── src/
│   │   │   ├── ...
│   ├── user-service/
│   │   ├── Dockerfile
│   │   ├── src/
│   │   │   ├── ...
│   ├── other-services/
│   │   ├── Dockerfile
│   │   ├── src/
│   │   │   ├── ...
├── docker-compose.yaml
└── README.md
```

# Using Docker Compose
You need to install docker desktop before you can use docker-compose.
You can find the download [here](https://www.docker.com/products/docker-desktop). 

Docker Compose should be included, but if it's not working try to 
install through `brew` `apt` `yum` `snap` or whatever package manager
you are presently uing on your OS.

### Pre-requisites:
- Docker
- Docker Compose

The current `docker-compose.yaml` should work out of the box with 
minimal modifications. 
The only thing you should need to change is the `build` path 
for each service. This should point to the `Dockerfile` for each service. 
For example, the `database-api` service should 
point to `./database-api/Dockerfile` and the `other-apis` service 
should point to `./other-apis/Dockerfile`

To run everything use the command

```
docker-compose up --build -d
```
`--build` will build the images for each service 
and `-d` will run the containers in the background (detached mode). 
You can omit `-d` if you want to see the logs in the terminal but
will need to `ctrl+c` to kill your container when you wish to exit.

Open the Docker Desktop app to see the containers running. 
If you are on a headless machine, you can run `docker ps -a`


# Access a containers shell

You cannot ssh into a container via traditional methods. 
You can however, run commands inside the container via exec. 

To do this, use the command `docker exec -it <container-name> <command>`. 

The `command` can be any normal shell command that you issue on your device
However you can open a terminal session by using `/bin/sh` or on suppoerted images
`/bin/bash`

For example, to run a bash shell inside the `database-api` container, 
use the command `docker exec -it database-api /bin/bash`. 

If you are using the Desktop version, 
you can select the container from the containers pane,
select the running container that you want to exec into
and click the `terminal` button to open a shell.

# Development of a service

When developing a new service, it is recommended to begin by using the
`docker init` command to create a new child-directory

`docker init` will populate the directory with all of the relevant docker
files and git files to create a new repository. It also has support
for various popular languages and frameworks making it easy to set up
boilerplate.

# Modules and Submodules

git and Github allow for use of submodules (nested repositories isolated
from their parent repostories).

If you are trying to maintain this root directory and make commits to it
submodules is the recommended feature to use.
For example, you want consistent developer configurations 
across multiple collaborators.

Submodules is a complex topic, and be better explained [here](https://www.git-scm.com/book/en/v2/Git-Tools-Submodules)

# Mounting working dirs as volumes:

Mounting directories is useful when working with scripting languages
that have dynamic updating via a debug mode. This is likely not available
on compiled languages.

When we are developing a service that does not have a debug mode 
or an update mode, we will need to rebuild the container on each
change. Rerunning the build via `docker-compose up --build` should
be sufficient enough.
