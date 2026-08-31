# pubcasefinder

# Prerequisites
* Docker Engine
* Docker Compose (Only required if the Docker Engine version is below v20.10.13)

# Download source code
Download source code from this repository
```
cd /your/path/src/
git clone --recursive https://github.com/PubCaseFinder/pubcasefinder.git
cd pubcasefinder
```
If you forget to add --recursive option, you can get submodules.
```
git submodule update --init --recursive --force
```

# Server environment
## Configuration environment
Create `.env` file and set values for your environment.
```
cp template.env .env
```
### `UID`
(default: None)

Host user id for the docker container. You can find your user id by `id -u`.

### `GID`

(default: None)

Host group id for the docker container. You can find your group id by `id -g`.

### `CONTAINER_NAME_APP`
(default: `pubcasefinder-app`)

The name of the docker container for the Flask application. Must be unique in the system.

### `IMAGE_NAME_APP`
(default: `pubcasefinder-app`)

The name of the docker image for the Flask application.

### `APP_PORT`
(default: `8000`)

Port for the Flask application to listen on. Must be unique in the system.

### `SPARQLIST_BASE_URL`
(default: `https://pubcasefinder.dbcls.jp/`)

Base URL for the SPARQLIST API. This Base URL is used when accessing the API from within the Flask application. It is not used for access from JavaScript or HTML.

### `CONTAINER_NAME_MYSQL`
(default: `pubcasefinder-mysql`)

The name of the docker container for the MySQL database. Must be unique in the system.

### `MYSQL_PORT`
(default: `3306`)

Port for the MySQL to listen on. Must be unique in the system.

### `MYSQL_ROOT_PASSWORD`
(default: None)

This variable is mandatory and specifies the password that will be set for the MySQL root superuser account.  
seeAlso: https://hub.docker.com/_/mysql

### `MYSQL_DATABASE`
(default: None)

This variable allows you to specify the name of a database to be created on image startup.  
seeAlso: https://hub.docker.com/_/mysql

### `MYSQL_USER`, `MYSQL_PASSWORD`
(default: None)

These variables used in conjunction to create a new user and to set that user's password.  
seeAlso: https://hub.docker.com/_/mysql


### `MYSQL_DATA_DIR`
(default: `./mysql/data`)

Directory for MySQL data storage. For better performance, it is recommended to place the database files on an SSD.

### `NGINX_PORT`
(default: `8888`)

Port for the nginx reverse proxy to listen on (used by `docker-compose_for_dev.yml`). Must be unique in the system.

### `NETWORK_NAME`
(default: None)

Specify the Docker network name for use with this docker-compose setup. If you have no particular preference, we recommend using either hogehoge or hogehoge_dev.

### `HANDSONTABLE_LICENSE_KEY`

Set the license key for Handsontable used in CaseSharing.
For more information, see https://handsontable.com/docs/javascript-data-grid/license-key/.

### `GOOGLE_DISCOVERY_URL`
(default: `"https://accounts.google.com/.well-known/openid-configuration"`)

OpenID Connect discovery document URL used for Google Sign-In.

### `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET`
(default: None)

OAuth 2.0 client credentials issued by Google, used for the application's login feature.  
seeAlso: https://console.cloud.google.com/apis/credentials

### `GOOGLE_FORM_SECRET_KEY`
(default: sample value provided in `template.env`)

Shared secret used to validate requests submitted to the account registration form endpoint. Replace the sample value with your own secret for production use.

### `GOOGLE_FORM_URL_PCF`, `GOOGLE_FORM_URL_PSN`
(default: None)

URLs of the account registration forms for CaseSharing (PCF) and PhenotypeSearchNavigator (PSN) users respectively.

### `MAIL_SMTP_SERVER`
(default: `smtp.gmail.com`)

SMTP server used to send notification emails (e.g. account registration, group invitations).

### `MAIL_SMTP_PORT`
(default: `587`)

Port for the SMTP server specified in `MAIL_SMTP_SERVER`.

### `MAIL_FROM_NAME`, `MAIL_FROM_EMAIL`
(default: None)

Display name and email address used as the sender of notification emails.

### `MAIL_USERNAME`, `MAIL_PASSWORD`
(default: None)

Credentials used to authenticate with the SMTP server.

## Opration
NOTE: If you are using a version prior to Docker Compose v2.0.0, use the `docker-compose` command instead of `docker compose`
### Create and start container
```
docker compose up -d
```
### Check status
```
docker compose ps
NAME                      IMAGE                   COMMAND                   SERVICE   CREATED         STATUS         PORTS
pubcasefinder-app_dev     pubcasefinder-app_dev   "pipenv run uwsgi --…"   app       5 seconds ago   Up 4 seconds   0.0.0.0:8000->8000/tcp
pubcasefinder-mysql_dev   mysql:5.7.13            "docker-entrypoint.s…"   mysql     5 seconds ago   Up 4 seconds   0.0.0.0:3306->3306/tcp
```

### (Only for first time) define table on mySQL schema

Enter the container
```bash
docker exec -it pubcasefinder-app /bin/bash
```

run script (in the container)
```bash
cd /app
pipenv run python -m casesharing.api.define_table
```

### Access to application

Check the application page can be displayed from a browser on the port number specified in the `.env` file. e.g. `http://localhost:8000`


### Stop container
```
docker compose stop
```
If the source code is changed, it must be `stop` and then `start`; this can also be done with the `restart` command.

### Delete container
```
docker compose down
```
If `.env` or `docker-compose.yml` is changed, delete the container and start it with `up -d`


## Configuration environment
For more information about configuration of Google Authentication , [Login Section](docs/README-login.md)
