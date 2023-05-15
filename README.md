# docker-bugzilla
A docker container to get the lastest version of bugzilla from github and run.

It uses Ubuntu 20.04, Apache2 and an existing MariaDB.

## Running on ARM / RPi
This docker is created to run on a Raspberry Pi 4 using docker-compose. Currently it is in the build-up phase and the following sections of this REAMDE.md are not updated.

## Configuration
### Configure using environment variables

You can configure the container using environment variables (for example, if you use a `docker-compose` environment).

Following environment variables are mandatory to be provided:
* **BUGZILLA_DB_HOST**: MySQL/MariaDB/PGSql Database hostname
* **BUGZILLA_DB_DRIVER**: DB Driver mysql or pg
* **BUGZILLA_DB_PORT**: Port to DB
* **BUGZILLA_DB_USER**: Database Root User name
* **BUGZILLA_DB_PASS**: Database Root password
* **BUGZILLA_DB_NAME**: Database Name
* **SERVERADMIN_EMAIL**: email address of the serveradmin (i.e. webmaster@foo.com)
* **SERVERNAME**: the URL your bugzilla server answers to (i.e. http://mybugs.foo.com)
* **BUGZILLA_ADMIN_EMAIL**: the email address of the bugzilla admin account
* **BUGZILLA_ADMIN_PASS**: the password for the bugzilla admin account (use a good one, it will be online!!)
* **BUGZILLA_ADMIN_REALNAME**: the real name or initials that shall appear in the bug assignment lists for the admin account name

If any of these misses out, the startup of the docker fails fatally, and you should consider checking the logs of the docker container to see which variable wasn't recognized.


### Step 1 - Preparation Database
* creata a database in your local MariaDB server for a specific bugzilla user, ensure to create a strong password, and be sure that the database can be connected from other docker machines (you might want to create a docker network connecting your docker containers, see the docker or docker-compose documentation for details)


### Step 2 - Preparation Config

* create a docker-compose.yml file containing the necessary config to run your server

### Step 3 - start the database and server

* remember to have started your database server

* start bugzilla-docker by the '''docker-compose up <containername>''' command and observe the output for errors
* if that worked, exit with Ctrl-C and start it again with '''docker-compose up -d <containername>'''

## Migration
According to current 2020-12-05 bugzilla documentation, it is possible to migrate from any bugzilla revision to a newer one. 

I was migrating from an older version, and decided to only use following data from the old installation:
* the data directory
* the images derictory (the favicon lives there)

The localconfig file will be regenerated from new Environment Variables, see above.

Remember to ensure not running two bugzilla instances on one database (I am not sure if it would work to run 2 instances on the same database).

After copying the direcories to the target volume on your docker host, check that all VOLUMES are correctly assigned. Then start the docker and after the docker started successfully, check the docker logs for the bugzilla container for the log output. When migrating and upgrading to a newer version, some data might have changed.

For more detailes on migration through upgrading, refer to https://bugzilla.readthedocs.io/en/5.0/installing/upgrading.html.

## Backup
As the bugzilla directory and the databse are accesible from outside the docker, data and database can easily be backed up and restored, see https://bugzilla.readthedocs.io/en/5.0/installing/backups.html.

## Maintenance
Keep an eye with vulnerability lists, i.e. http://slashdot.org.

In order to update your bugzilla installation, run '''docker-compose exec <image name> cd /var/www/html/bugzilla/ && git pull --rebase'''
