fusionx1-docker-d8
==================

This repo contains a recipe for making a Docker container
running Drupal8, using Linux, Apache, MySQL, Memcache and SSH.
You can also use it on the Drupal Sprints for quickly starting
working on your Drupal8 git repo.
To use it, make sure you first [Install Docker](https://docs.docker.com/installation/).

# Quick 3 step instructions for single run:

## 1 - Install docker:

https://docs.docker.com/installation/

## 2 - Get the image and run it using port 80:

```
sudo docker run -i -t -p 80:80 fusionx1/d8
```
That's it!

## 3 - Visit [http://localhost/](http://localhost/) in your browser
using user/pass: admin/admin

## 4 - Manage MySQL in your browser

[http://localhost/adminer.php](http://localhost/adminer.php)

# SPRINTING

If you want **Code and Database persistence** with an already
existent Drupal8 code that you have on your computer, run it with:

```
cd; mkdir d8; cd d8

git clone --depth 5 --branch 8.0.x http://git.drupal.org/project/drupal.git

sudo docker run -it \
--volume=$HOME/d8/mysql:/var/lib/mysql \
--volume=$HOME/d8/drupal:/var/www/html \
-p 80:80 -p 3306:3306 fusionx1/d8
```

You can remove the local settings.php and the mysql directory
for a fresh Drupal8 install with existent code:
```
cd $HOME/d8
sudo rm -rf mysql/ repo/sites/default/settings.php
```


### Credentials:
* Drupal account-name=admin & account-pass=admin
* ROOT SSH/MYSQL PASSWORD will be on /mysql-root-pw.txt
* DRUPAL   MYSQL_PASSWORD will be on /drupal-db-pw.txt

#### How to go back to the last docker run?
```
sudo docker ps -al
(get the container ID)
sudo docker start -i -a (container ID)
```

### Example usage for testing:
Using docker exec {ID} {COMMAND}, to run your own commands.
```
~$ sudo docker run --name mydrupal8 -i -t -p 80:80 fusionx1/d8

~$ sudo docker exec mydrupal8  uptime
 10:02:59 up 16:41,  0 users,  load average: 1.17, 0.92, 0.76

~$ sudo docker exec mydrupal8 drush status | head
 Drupal version         :  8.0.0-beta12
 Site URI               :  http://default
 Database driver        :  mysql
 Database hostname      :  localhost
 Database port          :  3306
 Drupal bootstrap       :  Successful
 ```

#### You can also clone this repo somewhere and build it,
```
git clone https://github.com/fusionx1/fusionx-docker-d8.git
cd fusionx1-docker-d8
sudo docker build -t <yourname>/d8 .
```

Note1: you cannot have port 80 already used or the container will not start.
In that case you can start by setting: `-p 8080:80`

Note2: To run the container in the background
```
sudo docker run -d -t -p 80:80 <yourname>/d8
```

## More docker awesomeness

This will create an ID that you can start/stop/commit changes:
```
# sudo docker ps
ID                  IMAGE                   COMMAND               CREATED             STATUS              PORTS
538example20        <yourname>/d8:latest   /bin/bash /start.sh   3 minutes ago       Up 6 seconds        80->80
```

Start/Stop
```
sudo docker stop container-name
sudo docker start container-name
```

Commit the actual state to the image
```
sudo docker commit name <yourname>/d8
```

Starting again with the commited changes
```
sudo docker run -d -t -p 80:80 <yourname>/d8 /start.sh
```

Shipping the container image elsewhere
```
sudo docker push  <yourname>/d8
```

You can find more images using the [Docker Index][docker_index].

### Clean up
While i am developing i use this to rm all old instances
```
sudo docker ps -a | awk '{print $1}' | grep -v CONTAINER | xargs -n1 -I {} sudo docker rm {}
```

### Known Issues
* Warning: This is still in development and ports shouldn't
be open to the outside world.

