## Prerequisites

0. Clone this repo
1. Install docker - https://www.docker.com/community-edition
2. You can read the docker documentation to have a better overview - https://docs.docker.com/get-started/part2/
3. If your using the [epfl registry](https://ic-registry.epfl.ch), login there, create a project and make it public. In the rest of the tutorial `registryUrl` refers to `ic-registry.epfl.ch/projectName` where `projectName` is the project you just created. You can also use other public registries like https://hub.docker.com.


### Create a file Dockerfile
```sh
$ vi Dockerfile

# Image based on a ubuntu
FROM ubuntu

# File Author / Maintainer
MAINTAINER Firstname Name <Firstname.Name@domain.tld>

# Update the repository sources list
RUN apt-get update

# Install apache2 php7
RUN apt-get install -y apache2 php7.0 libapache2-mod-php7.0 php7.0-cli php7.0-common php7.0-mbstring php7.0-gd php7.0-intl php7.0-xml php7.0-mysql php7.0-mcrypt php7.0-zip  && apt-get clean
```

### Build the image
```sh
$ docker build -t registryURL/ubuntu-apache-php-mysql  .
Sending build context to Docker daemon  3.072kB
Step 1/4 : FROM ubuntu
latest: Pulling from library/ubuntu
ae79f2514705: Pull complete 
....
Processing triggers for libapache2-mod-php7.0 (7.0.22-0ubuntu0.16.04.1) ...
 ---> e0b6bbe421aa
Removing intermediate container 29f31bd4b125
Successfully built e0b6bbe421aa
Successfully tagged registryURL/ubuntu-apache-php-mysql
```

### Test the image
```sh
$ docker run -ti registryURL/ubuntu-apache-php-mysql
root@7af8bd967f66:/# php -v
PHP 7.0.22-0ubuntu0.16.04.1 (cli) ( NTS )
Copyright (c) 1997-2017 The PHP Group
Zend Engine v3.0.0, Copyright (c) 1998-2017 Zend Technologies
    with Zend OPcache v7.0.22-0ubuntu0.16.04.1, Copyright (c) 1999-2017, by Zend Technologies
root@7af8bd967f66:/# exit
exit
```

### Host the image on our private registry

```sh
$ docker login registryURL
Authenticating with existing credentials...

Login Succeeded
$ docker push registryURL/ubuntu-apache-php-mysql
The push refers to a repository [registryURL/ubuntu-apache-php-mysql]
....
0f5ff0cf6a1c: Pushed 
latest: digest: sha256:cefd100e98caba89e973c749e9fe6373372acb213d8d6ca33b11692a24bf2462 size: 1781
```
