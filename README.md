#osx-docker-apache-php, a.k.a teammazehall/osx-docker-apache-php
    
    Out-of-the-box Apache+PHP Docker image that *just works* on Mac OS X. 
    Including write support for mounted volumes (Website).
    No matter if using the official boot2docker or having Vagrant in the stack, as well.

osx-docker-apache-php, which is known as 
[teammazehall/osx-docker-apache-php](https://registry.hub.docker.com/u/teammazehall/osx-docker-apache-php/) 
is a reduced fork of 
[dgraziotin/apache-php](https://github.com/dgraziotin/apache-php), 
which is an "Out-of-the-box LA_P image (PHP+MySQL) for Docker". 

Some info about osx-docker-apache-php:

- It is based on [phusion/baseimage:latest](http://phusion.github.io/baseimage-docker/)
  instead of ubuntu:trusty.
- It works flawlessy regardless of using boot2docker standalone or with Vagrant. You will need to set three enrironment varibles, though.
- It fixes OS X related [write permission errors for Apache](https://github.com/boot2docker/boot2docker/issues/581)
- It lets you mount OS X folders *with write support* as volumes for
  - The website
- It is documented for less advanced users (like me)


##Usage

    If using Vagrant, please see the extra steps in the next subsection.

docker build -t youruser/apache-php .

If you wish, you can push your new image to the registry:

    docker push youruser/apache-php

Otherwise, you are free to use teammazehall/osx-docker-apache-php as it is provided. Remember first
to pull it from the Docker Hub:

    docker pull teammazehall/osx-docker-apache-php

###Vagrant

If, for any reason, you would rather use Vagrant (I suggest using [AntonioMeireles/boot2docker-vagrant-box](https://github.com/AntonioMeireles/boot2docker-vagrant-box)), you need to add the following three variables when running your box:



###Running your Apache+PHP docker image

If you start the image without supplying your code, e.g.,

    docker run -t -i -p 80:80 --name website teammazehall/osx-docker-apache-php

At http://[boot2docker ip, e.g., 192.168.59.103] you should see an 
"Hello world!" page.


###Loading your custom PHP application

In order to replace the _Hello World_ application that comes bundled with this 
docker image, my suggested layout is the following:

- _Project name_ folder
  - app subfolder

The app folder should contain the root of your PHP application.

Run the following code from within the _Project name_ folder.

    docker run -i -t -p "80:80" -v ${PWD}/app:/app --name yourwebapp teammazehall/osx-docker-apache-php

Test your deployment:

    http://[boot2docker ip]


##Environment description


###The /app folder

Apache is configured to serve the files from the `/app` folder, which is a symbolic
link to `/srv/www/html`. In osx-docker-apache-php, the apache user `www-data` 
has full write permissions to the `app` folder.

###Apache

Apache is pretty much standard in this image. It is configured to serve the Web app
at `app` as `/` and phpMyAdmin as `/phpmyadmin`. Mod rewrite is enabled.

Apache runs as user www-data and group staff. The write support works because the
user www-data is configured to have the same user id as the one employed by boot2docker (1000).

##Environment variables

- `PHP_UPLOAD_MAX_FILESIZE="10M"` will change PHP upload_max_filesize config value
- `PHP_POST_MAX_SIZE="10M"` will change PHP post_max_size config value

Set these variables using the `-e` flag when invoking the `docker` client.

    docker run -i -t -p "80:80"-v ${PWD}/app:/app -e PHP_UPLOAD_MAX_FILESIZE="10M" --name yourwebapp teammazehall/osx-docker-apache-php