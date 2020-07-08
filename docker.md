Start
-----

    docker ps                       --> list running containers
    docker ps -a                    --> list all containers
    docker run -d mongo             --> run container in background
    docker run -p 27017:2017 -d mongo  --> run container in background publishing port
    docker run --name some_name_for_the_container -d mongo
    docker logs -f container_id

Assigning Storage
-----------------

1. Create a data directory on a suitable volume on your host system, e.g. `/my/own/datadir`.

2. Start your mongo container like this:

    $ docker run --name some-mongo -v /my/own/datadir:/data/db -d mongo


Run MySQL in a Container
------------------------

    $ docker run --name some-mysql -p 3306:3306 -v /my/own/datadir:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag

    $ docker run --name some-mysql -p 3306:3306 -v /my/own/datadir:/var/lib/mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=yes -d mysql


Connect to MySQL
----------------

mysql 8 requires some mumba-jumbo to make it work from the start. Run `docker exec -it container_id bash`  and after execute this:

    mysql -u root -p mysql

You will open a mysql console. After, run this:

    GRANT ALL PRIVILEGES ON *.* TO 'root'@'%';
    use mysql;
    UPDATE mysql.user SET host='%' WHERE user='root';

Type Ctrl+D and restart mysql with the following command: mysqld restart

[Credits](https://stackoverflow.com/a/53063112)


Cleaning Up Containers
----------------------
* Kill all Running Docker Containers
    * `docker kill $(docker ps -q)`
* Delete all Stopped Docker Containers
    * `docker rm $(docker ps -a -q)`

Cleaning Up Images
------------------
* Remove a Docker Image
    * `docker rmi image_name`
* Delete Untagged (dangling) Images
    * `docker rmi $(docker images -q -f dangling=true)`
* Delete all Images
    * `docker rmi $(docker images -q)`

Cleaning Up Volumes
-------------------
* Remove all dangling volumes
    * `docker volume rm $(docker volume ls -f dangling=true -q)`

Pruning
-------
There's a set of handy 'prune' commands available that can be used for cleanup of unused images, containers and volumes.

They basically do the same things as described in this lecture but with a one simple command.

For example, instead of

    docker volume rm $(docker volume ls -f dangling=true -q)

you can just use

    docker volume prune

This will remove all unused volumes.

There's even a "clean all" command docker system prune that will remove all unused objects altogether (add --volumes to also remove volumes).

[Further reading](https://docs.docker.com/config/pruning/)


[Docker Cheat Sheet for Spring Developers](https://springframework.guru/docker-cheat-sheet-for-spring-devlopers/)

