# WordPress Docker Mini-Stack

This Docker stack initializes the following services:

- **WordPress** php-fpm image for the WordPress installation. Themes and plugins can be stored in code under the ./wordpress directory.
- **MariaDB** service for the database.
- **Redis** for caching alongside php object cache.

This repository is meant to act as a template for setting up your WordPress stack.

## Initial Setup

**Step 0**: (optional) Update your ```.env``` dotfile for the enviornment that your working in (development, staging, production).

**Step 1**: Start docker and launch this stack using ```docker compose up -d```.

### Step 1: Configuration

#### Environment Configuration

This stack can be configured for different environments using the ```.env``` dotfile. See ```.env-template``` for all variables available. Copy ```.env-template``` to ```.env``` and update as necessary. This is where you change database username and password, update nginx configuration, specify which docker image versions to use for each service, etc.

#### Service Configuration

Service configuration can be adjusted under ```/.docker```. You can adjust settings like php memory, nginx conf, mysql memory limit, and etc.

```
.docker
├── mysql
│   └── my.cnf
├── nginx
│   └── conf.d
│       └── default.conf
└── php
    └── custom.ini
```

### Step 2: Start Docker Stack

In a development environment, you can download [Docker Desktop](https://www.docker.com/products/docker-desktop). On other systems, you will need to install Docker Engine and start the dameon.

Once Docker has been started, you can issue ```docker compose up -d``` and the stack will start. Please see below for more information on using docker compose.

## Docker Compose Basics

View the [docker compose documentation.](https://docs.docker.com/compose/)

Common commands to use on a docker stack.

```
docker compose up -d            # start services

docker compose ps               # view service info

docker compose logs -f          # follow docker logs

docker compose stop             # stop containers

docker compose down             # remove containers
```

## Administration Scripts

This repository includes various scripts located under ```./scripts``` to administer backups, etc. These scripts rely on a ```.env``` file in the root directory of your project. Scripts need to be run from the root diretory in order to read the ```.env``` file.

```
sh ./scripts/backup-volumes.sh  # Backup traefik, wordpress, and database data

sh ./scripts/restore-volumes.sh # Restore traefik, wordpress, and database data

```

## Development Environment with Multipass

Using [Multipass](https://multipass.run/docs), it is easy to develop locally using this stack. Execute the included ```multipass.sh``` script to start an ubuntu based docker instance. This will install Docker with the compose plugin.

```
sh multipass.sh
```

In order to access your multipass ubuntu instance for development, you need to add a hosts entry. Get the IP address of your instance by running ```multipass ls``` and then add the IP address and domain name you want to access your instance from to your ```/etc/hosts``` file. For example:

```
multipass ls
docker-wordpress        Running           10.211.55.40     Ubuntu 21.10
                                          172.17.0.1
                                          10.1.0.1
                                          172.18.0.1
                                          172.19.0.1
```

Add the ip/domain entry to /etc/hosts.

```
10.211.55.40    wordpress.local
```

### Docker commands in Multipass

You can ssh into your multipass instance to execute docker compose commands:

```
multipass sh docker-wordpress       # docker-wordpress is the name of the
                                    # instance supplied in multipass.sh

cd wordpress                        # cd into the mounted directory

docker compose logs -f              # view the logs

```

You can also run the commands from your host machine but the syntax is much longer:

```
multipass exec docker-wordpress -- \
    bash -c 'cd /home/ubuntu/wordpress && docker compose logs -f'
```