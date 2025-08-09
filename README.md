# cloud-sql-docker-migrator
Easy Docker-based Google Cloud SQL db migration tool using cloud sql proxy and postgresql-client

## Overview:
This is a Docker-based tool that uses docker-compose.yml to instantiate three containers:

1. PostgreSQL
2. Google Cloud SQL Proxy #1 (source db = `source`)
3. Google Cloud SQL Proxy #2 (target db = `target`)

## Installation:
1. Set up the environment variables in .env (use .env.example as a guide) required to make the Google Cloud SQL Proxy servers point at their respective to/from targets

2. Fire up the docker image using `docker compose up -d` to push it to the background or `docker compose up` to watch the logs

3. Exec into the PostgreSQL client container using: 
```
# docker exec -it <postgresql docker instance (probably cloud-sql-docker-migrator-postgres-client-1)> bash
``` 

_Use a separate terminal if you are still watching the logs from step 2 OR use the "exec" function from your Docker Desktop app if available_
4. Create the database you want to migrate to (enter your password when prompted): 
```
#/ psql -h target-proxy -U <user_name> postgres
Password for user <user_name>: 
psql (xx.x)
Type "help" for help.

postgres=> CREATE DATABASE "<db_name>";
CREATE DATABASE
postgres=> exit
```
5. Run your migration; enter your password when prompted: 
```
#/ PGPASSWORD="<source_user_password>" pg_dump -h source-proxy -U <user_name> <db_name> | psql -h target-proxy -U <user_name> <db_name>
Password for user <user_name>: 
SET
SET
...
<lots of activity>
...
REVOKE
GRANT
#/ 
```
6. Voilá, your migration should be complete. Exit out of your docker instance (`exit` at the shell and then ctrl-C to shut down your containers, `docker compose down -v` to clear any created volumes).