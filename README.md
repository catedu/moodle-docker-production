# Moodle-docker-production: Docker Containers for Moodle

This repository contains Docker configuration aimed to provide a good starting point to install moodle in production using docker.

In Catedu we need to deploy several hundreds of Moodle. This repo aims to give us a solution to test locally our developments and to generate our docker images. Our final solution is being implemented in [moodle-docker-deploy repo](https://github.com/catedu/moodle-docker-deploy).


## Features

* Database servers: MySQL / MariaDB /PostGreSQL (MySQL in this repo)
* Last supported PHP version
* Zero-configuration approach
* All php-extensions (thanks to [moodlehq](https://github.com/moodlehq/moodle-php-apache))


## Missing Features

These features are out of the scope of this repo (you may have a look at [moodle-docker-deploy repo](https://github.com/catedu/moodle-docker-deploy)):

* Crontab configuration
* Auto backup 


## Prerequisites

* [Docker](https://docs.docker.com) and [Docker Compose](https://docs.docker.com/compose/) installed


## Quick start


```
cp env-sample .env
docker-compose up -d
```

Open browser webpage: http://localhost (be patient)


## Configuration

* Configure your moodle installation using an .env file
* We have two environments: production (https ready with letsencrypt) and development (default).

* Plugins or special configuration is done using shell scripts or php files. The moodle container executes them from init-scripts directory. We provide an example script (plugins-sampe.sh) file, and moodle image has [moosh binary](https://moosh-online.com/).


## Activate https

- Using [letsEncrypt](https://letsencrypt.org/), activating production environment:

  ```
  cp docker-compose.override_prod.yml docker-compose.override.yml
  docker-compose up -d
  ```
- Modify .env file

  ```
  MOODLE_URL=https://localhost
  SSL_PROXY=true
  ```
  
## How to create a new image

- Create a new directory with the selected Moodle version. For example for Moodlle 3.9.0 and using nginx-fpm (best choice):

```
cd ${REPO_DIR}
cp -r nginx-fpm/3.8.3 nginx-fpm/3.9.0
```

- Modify files (Dockerfile, php extensions file, entrypoint...)
- Create a workflow yml file for your version at .github/workflows
- Commit and push so Docker Hub knows about the change to build new public image
- if you create the workflow after pushing changes you will have to modify a file, commit and push again in order to trigger the workflow

## Contributions

Are extremely welcome!

## After installing. TODOs via web interface

* Need to upload users
