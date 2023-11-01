# Introduction to Microservices - Tasks

## Docker

### Before starting

If you do not have Docker installed yet, follow the instructions at https://docs.docker.com/get-docker/ to install it.

### Task D1: Docker Hello world!

The following command will download the hello-world:latest image and run it:

```sh
docker run hello-world
```

Now, check the status of the container:

```sh
docker container ls -a

CONTAINER ID  IMAGE     COMMAND    CREATED     STATUS           PORTS   NAMES
763d61bd1f66  hello-world  "/hello"   15 seconds ago  Exited (0) 14 seconds ago       angry_mestorf
```

Note that the status "Exited": the container is not running anymore.

### Task D2: Running Ubuntu

The following command will download the ubuntu:latest image and run it:

```sh
docker run ubuntu
```

> **Question:** Why is there no output as before?


Now, check the status of the container:

```sh
docker container ls -a

CONTAINER ID  IMAGE     COMMAND    CREATED     STATUS           PORTS   NAMES
252e90596cd3  ubuntu    "/bin/bash"  10 seconds ago  Exited (0) 11 seconds ago       confident_hopper
```

Note that the status "Exited": the container is not running anymore.

Try the `ls` without `-a`:

```sh
docker container ls

CONTAINER ID  IMAGE     COMMAND    CREATED     STATUS           PORTS   NAMES
```

> **Question:** Why is the output empty?

> **Hint:** `docker ps` and `docker ps -a` were the old way of checking the containers.
> They still work and can be found in many tutorials everywhere.

### Task D3: Running an interactive shell on Ubuntu

```sh
docker run -it ubuntu bash

root@1b308e8aa1a3:/# 
```

If you executed the previous task, the `ubuntu:latest` image is already on your computer and will not be downloaded again. You can interact with the shell.

Keep this shell running and open a new terminal window. Then type the following command:

```sh
docker container ls -a

CONTAINER ID  IMAGE     COMMAND    CREATED     STATUS           PORTS   NAMES
1b308e8aa1a3  ubuntu    "bash"    3 minutes ago  Up 3 minutes             clever_bartik
252e90596cd3  ubuntu    "/bin/bash"  13 minutes ago  Exited (0) 13 minutes ago       confident_hopper
```

Compare the two Ubuntu containers: one is still running.

Get back to the previous terminal and type `exit`:

```sh
root@1b308e8aa1a3:/# exit
```

Then, recheck the containers:

```sh
docker container ls -a   

CONTAINER ID  IMAGE     COMMAND    CREATED     STATUS           PORTS   NAMES
1b308e8aa1a3  ubuntu    "bash"    6 minutes ago  Exited (0) 11 seconds ago       clever_bartik
252e90596cd3  ubuntu    "/bin/bash"  17 minutes ago  Exited (0) 17 minutes ago       confident_hopper
```

They both exited.


### Task D4: Where are the images?

Try the command below:

```sh
docker image ls

REPOSITORY  TAG    IMAGE ID    CREATED    SIZE
ubuntu    latest  e4c58958181a  3 weeks ago  77.8MB
hello-world  latest  9c7a54a9a43c  6 months ago  13.3kB
```
