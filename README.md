# Metabase-migration-h2-to-MySQL
#### We are going to migrate a metabase server that is on a Docker container from h2 to a MariaDB MySQL database
## Previous steps:
#### install java
```bash
yum -y update
yum install java-1.8.0-openjdk
```
## Now we are ready to beging:
#### First step is access to the metabase files, in my case i have it in a docker container:
```bash
docker exec -it CONTAINER_ID sh
cd /metabase.db
```
###### "You should see the following files: metabase.db.h2.db  # Or metabase.db.mv.db depending on when you first started using Metabase."
#### Create Backup (safety first)
###### The file you need to copy is "metabase.db.h2.db" o "metabase.db.mv.db", in this case is in a container in docker.
```bash
mkdir -p /tmp/metabase
docker cp CONTAINER_ID:/metabase.db/metabase.db.mv.db /tmp/metabase
ls /tmp/metabase
```
#### Copy .jar from metabase and exec
```
docker cp CONTAINER_ID:/app/metabase.jar /tmp/metabase
```
#### Now you need to choose if your are migrating to mysql or to postgres
```bash
export MB_DB_TYPE=mysql or postgres
export MB_DB_DBNAME=metabase
export MB_DB_PORT=3306
export MB_DB_USER=YOUR_USER
export MB_DB_PASS=YOUR_DB_PASSWORD
export MB_DB_HOST=YOUR_DATABASE_HOST
```
#### start the migration, remember do not include .mv.db or .h2.db suffix
```bash
cd /tmp/metabase
java -jar metabase.jar load-from-h2 /tmp/metabase/metabase.db
java -jar /opt/metabase/metabase.jar load-from-h2 /tmp/metabase.db
```
#### Now you need to stop docker container and run the following command:
```bash
docker run -d -p 80:3000 -e "MB_DB_TYPE=mysql" -e "MB_DB_DBNAME=metabase" -e "MB_DB_PORT=3306" -e "MB_DB_USER=YOUR_USER" -e "MB_DB_PASS=YOUR_DB_PASSWORD" -e "MB_DB_HOST=YOUR_DATABASE_HOST" --name metabase-mysql metabase/metabase
```
##### Done!, you have completed the migration of your Metabase from a docker container in h2 to a instance with MySQL
