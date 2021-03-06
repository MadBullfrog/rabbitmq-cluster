Modified from https://github.com/harbur/docker-rabbitmq-cluster to update to 3.6.1

Docker images to run RabbitMQ cluster. It extends the official image with a rabbitmq-cluster script that does the magic.

# Building

Once you clone the project locally use [captain](https://github.com/harbur/captain) to build the image or do with docker:

```
docker build -t grandmore/rabbitmq-cluster .
```

# Running with docker-compose

If you want to run the cluster on one machine use [docker-compose](https://github.com/docker/compose/)

```
docker-compose up -d
```

By default 3 nodes are started up this way:

```
rabbit1:
  image: grandmore/rabbitmq-cluster
  hostname: rabbit1
  environment:
    - ERLANG_COOKIE=abcdefg
  ports:
    - "5672:5672"
    - "15672:15672"
rabbit2:
  image: grandmore/rabbitmq-cluster
  hostname: rabbit2
  links:
    - rabbit1
  environment:
    - ERLANG_COOKIE=abcdefg
    - CLUSTER_WITH=rabbit1
    - ENABLE_RAM=true
    - RAM_NODE=true
  ports:
    - "5673:5672"
    - "15673:15672"
rabbit3:
  image: grandmore/rabbitmq-cluster
  hostname: rabbit3
  links:
    - rabbit1
    - rabbit2
  environment:
    - ERLANG_COOKIE=abcdefg
    - CLUSTER_WITH=rabbit1
  ports:
    - "5674:5672"
```

# Setting default user and password
If you wish to change the default username and password of guest / guest, you can do so with the RABBITMQ_DEFAULT_USER and RABBITMQ_DEFAULT_PASS environmental variables:
	
$ docker run -d --hostname my-rabbit --name some-rabbit -e RABBITMQ_DEFAULT_USER=user -e RABBITMQ_DEFAULT_PASS=password rabbitmq:3-management
You can then go to http://localhost:8080 or http://host-ip:8080 in a browser and use user/password to gain access to the management console


If needed, additional nodes can be added to this file.

Once cluster is up:
* The management console can be accessed at `http://hostip:15672`
* The connection host should look like this: `hostip:5672,hostip:5673,hostip:5674`

# Credits

* Inspired by https://github.com/bijukunjummen/docker-rabbitmq-cluster
