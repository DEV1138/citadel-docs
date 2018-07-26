# Build Citadel Using Docker

## Set up Docker

Building citadel requires that you have **Docker CE** installed on the build host.  The version of Docker
provided by your Linux distribution will probably not work and you should follow the following instructions
instead:

* [Installing Docker CE on Debian](https://docs.docker.com/install/linux/docker-ce/debian/)
* [Installing Docker CE on Fedora](https://docs.docker.com/install/linux/docker-ce/fedora/)

After installing Docker you may need to start the docker daemon.

```shell
$ systemctl start docker
```

If you want the docker daemon to start automatically on boot you also need to enable it.

```shell
$ systemctl enable docker
```

You may optionally add your user account to the `docker` group so that you can issue docker commands without using
sudo.  

**Warning:** This is more convenient but be careful because containers can be configured to share any file on the host. 
A user with access to the docker group can easily escalate privileges to root while the docker daemon is running.

```shell
$ sudo usermod -aG docker $USER
```

## Build Citadel

A `Makefile` is provided which only contains a couple of simple targets that execute docker commands to set up and run the 
builder container.

The project uses git submodules to track openembedded layers it depends upon.  After cloning this repository you will need to 
retrieve the dependent submodules with the following command:

```shell
$ make update-submodules
```

To create the builder docker image use the following command.  You only need to do this one time, but if you run it again
Docker will realize that the Dockerfile has not changed and do nothing.

```shell
$ make docker-image
```

To list available make targets, run `make help` or just `make` as this is the default target:

```shell
$ make help
```

To run a shell inside the docker build container:

```shell
$ make docker-shell
```

The shell will run in the build directory and be configured to run build commands with `bitbake`.  

To build a full citadel image:

```shell
$ make citadel-image
```

The build will take several hours the first time, but for later builds the build system will use cached artifacts stored 
in `citadel/build/sstate-cache` for components that have not changed and new builds will usually only take a few minutes.
