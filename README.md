# The Ultimate Docker Cheatsheets

start a container from image   
`docker run -d -it --name container_name_given image_name bash`  
`-i interactive` `-t tty` `-d detach` `bash command`    
run container  
`docker exec -it container_name_given bash`

run container as root   
`docker exec -u root -t -i container_id /bin/bash`  

save container as image  
`docker commit container_name image_name_whatever_you_give`  

 save image to file  
 `docker save image_name > file_name.tar`  
docker push  
`docker login`  
if image name xpt2csv then tag like this  
` docker image tag xpt2csv yousuf28/xpt2csv`  
 then   
 `docker push yousuf28/xpt2csv`  
 
### volume
`docker run -v <host_directory>:<container_directory> <image>`  
To mount the current directory into the container at a specified path,  
`docker run -v .:/path/to/directory <image>`  
To mount the current directory at the same path into the container 
might have to put "$(pwd)"  like this with double quote.  
`docker run -v $(pwd):$(pwd) <image>`   
on windows   
`docker run -v C:\\Users\\user\\work:/work <image>`  
or  
`docker run -v //c/Users/user/work:/work <image>`   
Note that when using the $(pwd) command substitution, you will have to prepend a __slash__ character to this expression in order for it to work:  
`docker run -v /$(pwd):/work <image>`  
`docker run -d -it  --name xpt5 -v "/$(pwd)":/home  yousuf28/xpt2csv`


### docker compose 
```yml
docker compose up (in docker compose dir)
docker ps -a
docker exec -it container_id from above
```

[see end of page for windows](https://www.warp.dev/terminus/docker-run-volume)  



Full Dockercheatsheets 
![full](https://github.com/sangam14/dockercheatsheets/blob/master/dockercheatsheet8.png)

 Container Management Commands 
![container_management](https://github.com/sangam14/dockercheatsheets/blob/master/dockercheatsheet1.png)

Inspecting The Container 
![Inspecting The Container](https://github.com/sangam14/dockercheatsheets/blob/master/dockercheatsheet3.png)
![Interacting with Container1](https://github.com/sangam14/dockercheatsheets/blob/master/dockercheatsheet4.png)
 Image Management Commands 
 ![image management commands](https://github.com/sangam14/dockercheatsheets/blob/master/dockercheatsheet5.png)
 Image Transfer Comnands 
 ![Image Transfer Comnands](https://github.com/sangam14/dockercheatsheets/blob/master/dockercheatsheet6.png)
 Builder Main Commands
![Builder Main Commands](https://github.com/sangam14/dockercheatsheets/blob/master/dockercheatsheet7.png)





# docker CLI 

Manage images
-------------

### `docker build`

```yml
docker build [options] .
  -t "app/container_name"    # name
```

Create an `image` from a Dockerfile.


### `docker run`

```yml
docker run [options] IMAGE
  # see `docker create` for options
```

Run a command in an `image`.

Manage containers
-----------------

### `docker create`

```yml
docker create [options] IMAGE
  -a, --attach               # attach stdout/err
  -i, --interactive          # attach stdin (interactive)
  -t, --tty                  # pseudo-tty
      --name NAME            # name your image
  -p, --publish 5000:5000    # port map
      --expose 5432          # expose a port to linked containers
  -P, --publish-all          # publish all ports
      --link container:alias # linking
  -v, --volume `pwd`:/app    # mount (absolute paths needed)
  -e, --env NAME=hello       # env vars
```

#### Example

```
$ docker create --name app_redis_1 \
  --expose 6379 \
  redis:3.0.2
```

Create a `container` from an `image`.

### `docker exec`

```yml
docker exec [options] CONTAINER COMMAND
  -d, --detach        # run in background
  -i, --interactive   # stdin
  -t, --tty           # interactive
```

#### Example

```
$ docker exec app_web_1 tail logs/development.log
$ docker exec -t -i app_web_1 rails c
```

Run commands in a `container`.


### `docker start`

```yml
docker start [options] CONTAINER
  -a, --attach        # attach stdout/err
  -i, --interactive   # attach stdin

docker stop [options] CONTAINER
```

Start/stop a `container`.


### `docker ps`

```
$ docker ps
$ docker ps -a
$ docker kill $ID
```

Manage `container`s using ps/kill.

Images
------

### `docker images`

```sh
$ docker images
  REPOSITORY   TAG        ID
  ubuntu       12.10      b750fe78269d
  me/myapp     latest     7b2431a8d968
```

```sh
$ docker images -a   # also show intermediate
```

Manages `image`s.

### `docker rmi`

```yml
docker rmi b750fe78269d
```

Deletes `image`s.

Also see
--------

 * [Getting Started](http://www.docker.io/gettingstarted/) _(docker.io)_
 
 
# Dockerfile

### Inheritance

```docker
FROM ruby:2.2.2
```

### Variables

```docker
ENV APP_HOME /myapp
RUN mkdir $APP_HOME
```

### Initialization

```docker
RUN bundle install
```

```docker
WORKDIR /myapp
```

```docker
VOLUME ["/data"]
# Specification for mount point
```

```docker
ADD file.xyz /file.xyz
COPY --chown=user:group host_file.xyz /path/container_file.xyz
```

### Onbuild

```docker
ONBUILD RUN bundle install
# when used with another file
```

### Commands

```docker
EXPOSE 5900
CMD    ["bundle", "exec", "rails", "server"]
```

### Entrypoint

```docker
ENTRYPOINT ["executable", "param1", "param2"]
ENTRYPOINT command param1 param2
```

Configures a container that will run as an executable.

```docker
ENTRYPOINT exec top -b
```

This will use shell processing to substitute shell variables, and will ignore any `CMD` or `docker run` command line arguments.

### Metadata

```docker
LABEL version="1.0"
```

```docker
LABEL "com.example.vendor"="ACME Incorporated"
LABEL com.example.label-with-value="foo"
```

```docker
LABEL description="This text illustrates \
that label-values can span multiple lines."
```

## See also

- <https://docs.docker.com/engine/reference/builder/>


# docker-compose


### Basic example

```yaml
# docker-compose.yml
version: '2'

services:
  web:
    build: .
    # build from Dockerfile
    context: ./Path
    dockerfile: Dockerfile
    ports:
     - "5000:5000"
    volumes:
     - .:/code
  redis:
    image: redis
```

### Commands

```sh
docker-compose start
docker-compose stop
```

```sh
docker-compose pause
docker-compose unpause
```

```sh
docker-compose ps
docker-compose up
docker-compose down
```

## Reference
{: .-three-column}

### Building

```yaml
web:
  # build from Dockerfile
  build: .
```

```yaml
  # build from custom Dockerfile
  build:
    context: ./dir
    dockerfile: Dockerfile.dev
```

```yaml
  # build from image
  image: ubuntu
  image: ubuntu:14.04
  image: tutum/influxdb
  image: example-registry:4000/postgresql
  image: a4bc65fd
```

### Ports

```yaml
  ports:
    - "3000"
    - "8000:80"  # guest:host
```

```yaml
  # expose ports to linked services (not to host)
  expose: ["3000"]
```

### Commands

```yaml
  # command to execute
  command: bundle exec thin -p 3000
  command: [bundle, exec, thin, -p, 3000]
```

```yaml
  # override the entrypoint
  entrypoint: /app/start.sh
  entrypoint: [php, -d, vendor/bin/phpunit]
```

### Environment variables

```yaml
  # environment vars
  environment:
    RACK_ENV: development
  environment:
    - RACK_ENV=development
```

```yaml
  # environment vars from file
  env_file: .env
  env_file: [.env, .development.env]
```

### Dependencies

```yaml
  # makes the `db` service available as the hostname `database`
  # (implies depends_on)
  links:
    - db:database
    - redis
```

```yaml
  # make sure `db` is alive before starting
  depends_on:
    - db
```

### Other options

```yaml
  # make this service extend another
  extends:
    file: common.yml  # optional
    service: webapp
```

```yaml
  volumes:
    - /var/lib/mysql
    - ./_data:/var/lib/mysql
```

## Advanced features


### Labels

```yaml
services:
  web:
    labels:
      com.example.description: "Accounting web app"
```

### DNS servers

```yaml
services:
  web:
    dns: 8.8.8.8
    dns:
      - 8.8.8.8
      - 8.8.4.4
```

### Devices

```yaml
services:
  web:
    devices:
    - "/dev/ttyUSB0:/dev/ttyUSB0"
```

### External links

```yaml
services:
  web:
    external_links:
      - redis_1
      - project_db_1:mysql
```

### Hosts

```yaml
services:
  web:
    extra_hosts:
      - "somehost:192.168.1.100"
```

## Contributor - 

Sangam biradar - smbiradar14@gmail.com - https://discord.kubedaily.live 


