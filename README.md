# Docker

<small><i>Inspired by <https://training.play-with-docker.com/beginner-linux/></small></i>

## Commands

This section contains a list of useful docker commands.
It is only for reference.
You don't have to type them (yet).

Parameters in angle brackets are replaced by user input.

### Shell

Run a shell from a container image.

```sh
docker container run -it --rm <image> sh
```

| Flag      | Meaning                                       |
| --------- | --------------------------------------------- |
| -it       | Interactive                                   |
| --rm      | Remove container and volumes on exit          |
| `<image>` | Name of image like `ubungu`, `alpine` etc.    |
| sh        | Command to execute. `sh` is a primitive shell |

### Show containers

Show running containers.

```sh
docker ps
```

Show all containers, even those that are not actively running.

```sh
docker ps
```

### Shell in running container

```sh
docker exec -it <id or name> sh
```

You can find ID and Name with `docker ps` command.

### Show logs

```sh
docker logs <id or name>
```

### Build an image

```sh
docker image build <directory>
```

Where `<directory>` is a folder containing a `Dockerfile`.

It will create with a long HEX code as name.

You can name and version an image by tagging it.

```sh
docker image build --tag <name>:<version> <directory>
```

### Clean up

To stop a running container, do:

```sh
docker stop <id or name>
```

You can remove a container once it is no longer up (running).

```sh
docker rm <id or name>
```

To free up space on your computer, you can remove all unused objects (including
volumes).

```sh
docker system prune --volumes
```

**Warning: can be dangerous since it deletes all data that is stored within
containers.**

## Base images

Containers are based on a base image.
Different base images exists for different [Linux
distributions](https://en.wikipedia.org/wiki/Linux_distribution).

On most Linux systems you can find out what distribution and version it is by
running `cat /etc/issue`.
Let's try it out on a couple of different base images.

**From now on you should try the commands out in your shell.**

```sh
docker container run -it --rm ubuntu sh
```

Then:

```sh
cat /etc/issue
```

<kbd>CTRL</kbd>+<kbd>d</kbd> to exit out of the container.
You should then see your normal prompt.

Many examples use [Alpine Linux](https://www.alpinelinux.org/) as base image
since it has a small footprint.
Let's try it out!

```sh
docker container run -it --rm alpine sh
```

Then check the version with:

```sh
cat /etc/issue
```

<kbd>CTRL</kbd>+<kbd>d</kbd> to exit out of the container.

You can also run a specific version of Alpine.

```sh
docker container run -it --rm alpine:3.17 sh
cat /etc/issue
```

<kbd>CTRL</kbd>+<kbd>d</kbd> to exit.

## Database in a container

Let's put some of the Docker commands to use by spinning up a database.

```sh
docker run --name mydb -e POSTGRES_PASSWORD=mysecret -P -d postgres
```

| Parameter | Meaning                                       |
| --------- | --------------------------------------------- |
| --name    | Give the container a name (`mydb`)            |
| -e        | Set an environment variable for the container |
| -d        | Run in background (short for "detach")        |

You now have a [PostgreSQL](https://www.postgresql.org/) database running
within a docker container.
PostgreSQL (or postgres for short) is the database system you will be using
throughout this semester.

You could have started MySQL database instead by running `docker run --name
mydb -e MYSQL_ROOT_PASSWORD=secret -d mysql`.

With the power of container you are able to run a bunch of different services
without installing anything directly on your host OS.
This becomes even more powerful when you have projects that rely on different
versions of the same database engine.

You can use the `psql` command from within the container to configure and query
the database.
Try it out!

```sh
docker exec -it mydb psql -U postgres postgres
```

You should see a `postgres=#` prompt.
You can find the version of postgres that is running with:

```sql
SELECT version();
```

Then <kbd>CTRL</kbd>+<kbd>d</kbd> to exit.
You should see your normal command line prompt again.

If the database had tables you would have been able to run queries against it.

## Web page in container

In this part you will start a simple web server in a container.

Open a terminal.
Navigate to a folder where you can store some temporary files for this guide.

Clone a sample repository.

```sh
git clone https://github.com/dockersamples/linux_tweet_app
cd linux_tweet_app
```

The repository comes with a Dockerfile, which is a recipe for a container
image.

To view it type:

```sh
cat Dockerfile
```

It uses the latest version of nginx as its base image.
The only thing the recipe does is:

1. Copy `index.html` and `index.png` into the container images
2. Expose the defaults ports for HTTP and HTTPS (web traffic)
3. Start nginx

To build an image from the Dockerfile you simply run:

```sh
docker build .
```

**Notice the `.` (dot)**

The name of image is the hex value you see right after `writing image sha256:`
in the output.
Not very friendly, so let's give it a tag.

```sh
docker image build --tag linux_tweet_app:1.0 .
```

You can then run a container from the image, using:

```sh
docker container run -d -p 80:80 --name linux_tweet_app linux_tweet_app:1.0
```

The reason you see `linux_tweet_app` twice in the command is because we name
the container `linux_tweet_app` and the image we use for the container is
tagged `linux_tweet_app:1.0`.

[Open sample application](http://localhost)

![Screenshot of sample application](./sample-app.png)

_Didn't bother creating my own app for this, so just used one from somebody
else._

## Clean up

To clean up, you first need to stop any active containers.

```sh
docker ps
```

Then for each, do:

```sh
docker stop <id>
```

Or, if you are in a bash shell (like git-bash), then you can just do `docker
stop $(docker ps -a -q)` to list and stop all containers.

To remove all stopped containers and free up space, do:

```sh
docker system prune
```
