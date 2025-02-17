# Installation

listmonk is a simple binary application that requires a Postgres database instance to run. The binary can be downloaded and run manually, or it can be run as a container with Docker compose.

See the "[Tutorials](#tutorials)" section at the bottom for detailed guides. 

## Binary
1. Download the [latest release](https://github.com/knadh/listmonk/releases) and extract the listmonk binary. `amd64` is the main one. It works for Intel and x86 CPUs.
1. `./listmonk --new-config` to generate config.toml. Edit the file.
1. `./listmonk --install` to install the tables in the Postgres DB (⩾ 12).
1. Run `./listmonk` and visit `http://localhost:9000` to create the Super Admin user and login.

!!! Tip
    To set the Super Admin username and password during installation, set the environment variables:
    `LISTMONK_ADMIN_USER=myuser LISTMONK_ADMIN_PASSWORD=xxxxx ./listmonk --install`


## Docker

The latest image is available on DockerHub at `listmonk/listmonk:latest`

The recommended method is to download the [docker-compose.yml](https://github.com/knadh/listmonk/blob/master/docker-compose.yml) file, customize it for your environment and then to simply run `docker compose up -d`.

```shell
# Download the compose file to the current directory.
curl -LO https://github.com/knadh/listmonk/raw/master/docker-compose.yml

# Run the services in the background.
docker compose up -d
```

Then, visit `http://localhost:9000` to create the Super Admin user and login.

!!! Tip
    To set the Super Admin username and password during setup, set the environment variables (only the first time):
    `LISTMONK_ADMIN_USER=myuser LISTMONK_ADMIN_PASSWORD=xxxxx docker compose up -d`


------------

### Mounting a custom config.toml
The docker-compose file includes all necessary listmonk configuration as environment variables, `LISTMONK_*`.
If you would like to remove those and mount a config.toml instead:

#### 1. Save the config.toml file on the host

```toml
[app]
address = "0.0.0.0:9000"

# Database.
[db]
host = "listmonk_db" # Postgres container name in the compose file.
port = 5432
user = "listmonk"
password = "listmonk"
database = "listmonk"
ssl_mode = "disable"
max_open = 25
max_idle = 25
max_lifetime = "300s"
```

#### 2. Mount the config file in docker-compose.yml

```yaml
  app:
    ...
    volumes:
    - /path/on/your/host/config.toml:/listmonk/config.toml
```

#### 3. Change the `--config ''` flags in the `command:` section to point to the path

```yaml
command: [sh, -c, "./listmonk --install --idempotent --yes --config /listmonk/config.toml && ./listmonk --upgrade --yes --config /listmonk/config.toml && ./listmonk --config /listmonk/config.toml"]
```


## Compiling from source

To compile the latest unreleased version (`master` branch):

1. Make sure `go`, `nodejs`, and `yarn` are installed on your system.
2. `git clone git@github.com:knadh/listmonk.git`
3. `cd listmonk && make dist`. This will generate the `listmonk` binary.

## Release candidate (RC)

The `master` branch with bleeding edge changes is periodically built and published as `listmonk/listmonk:rc` on DockerHub. To run the latest pre-release version, replace all instances of `listmonk/listmonk:latest` with `listmonk/listmonk:rc` in the docker-compose.yml file and follow the Docker installation steps above. While it is generally safe to run release candidate versions, they may have issues that only get resolved in a general release.

## Helm chart for Kubernetes

![Version: 0.1.0](https://img.shields.io/badge/Version-0.1.0-informational?style=flat-square) ![Type: application](https://img.shields.io/badge/Type-application-informational?style=flat-square) ![AppVersion: 3.0.0](https://img.shields.io/badge/AppVersion-3.0.0-informational?style=flat-square)
