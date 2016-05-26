# DevOps Ubuntu Cheat Sheet

#### Initial
```
$ sudo apt-get update
$ sudo apt-get upgrade
$ sudo apt-get install python-psycopg2
$ sudo apt-get install libpq-dev
$ sudo apt-get install libjpeg-dev
```

#### Permision settings
```
$ sudo groupadd wwwusers
$ sudo adduser www-data wwwusers
$ sudo chgrp -R wwwusers /home/<project-name>/public/
$ sudo chmod -R 777 /home/<project-name>/public/
```

#### SCP (Secure copy) securely transferring computer files between a local host and a remote host or between two remote hosts.
```
# REMOTE to REMOTE:
$ scp username1@hostname1:/path/to/file username2@hostname2:/path/to/other/file
# REMOTE to LOCAL:
$ scp username@hostname:/path/to/remote/file /path/to/local/file
# LOCAL to REMOTE:
$ scp /path/to/local/file username@hostname:/path/to/remote/file
# Using with port no:
$ scp -P 1234 username@hostname:/path/to/remote/file /path/to/local/file
```

#### Unzip files
```
# Install 7zip:
$ sudo apt-get install 7zip unzip
# Example:
$ unzip nameofthefile -d directory
```

#### Manage files
```
# Move File:
$ mv /home/user/testfile /home/user/testfile2
```

#### PostgreSQL
```
$ sudo su - postgres
$ createdb mydb
$ createuser <myuser> -P
$ psql
$ GRANT ALL PRIVILEGES ON DATABASE mydb TO myuser;
```

#### PostgreSQL DUMP
```
# From REMOTE:
$ pg_dump --host=<host-name> --dbname=<db-name> --username=<db-username> --port=<port> > db.sql
# From LOCAL:
$ pg_dump <db-name> > db.sql
```

#### PostgreSQL RESTORE
```
# From LOCAL:
$ pg_restore "<db-name>" db.dump
# If fails:
$ cat db.dump | psql "<db-name>"
```

#### PostgreSQL 9.3 Config files Locations
```
$ /etc/postgresql/9.3/main/postgresql.conf
$ /etc/postgresql/9.3/main/pg_hba.conf
```

#### Updating PostgreSQL 9.3 to 9.4 on Ubuntu
```
# Saving your config files [Optional]:
$ sudo cp /etc/postgresql/9.3/main/postgresql.conf ~/path/to/location
$ sudo cp /etc/postgresql/9.3/main/pg_hba.conf ~/path/to/location
# Package repo (for apt-get)
$ wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
$ sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ precise-pgdg main" >> /etc/apt/sources.list.d/postgresql.list'
# Update the apt-get
$ sudo apt-get update
$ sudo apt-get upgrade
# Install postgres 9.4
$ sudo apt-get install postgresql-9.4 postgresql-server-dev-9.4 postgresql-contrib-9.4
# Get latest postgis for 9.4 (optional)
$ sudo apt-get install postgresql-9.4-postgis
# Dump your data
$ sudo su postgres
$ cd /mnt/dumps
$ /usr/lib/postgresql/9.4/bin/pg_dumpall > pre_upgrade_from_9.3_to_9.4.dump
```

#### Nginx
```
$ sudo service nginx start
$ sudo service nginx stop
$ sudo service nginx restart
$ sudo nano /etc/nginx/sites-available/<project-name>
$ sudo ln -s /etc/nginx/sites-available/<project-name> /etc/nginx/sites-enabled

# Test nginx for errors
$ sudo nginx -t
# Show recent logs
$ sudo cat /var/log/nginx/error.log
```
#### Setting up uWSGI
```
$ sudo pip install uwsgi
$ sudo mkdir -p /etc/uwsgi/sites
$ cd /etc/uwsgi/sites
$ sudo nano <project-name>.ini
```
```
[uwsgi]
project = <project-name>
base = /home

chdir = %(base)/%(project)
home = %(base)/Env/%(project)
module = %(project).wsgi:application

master = true
processes = 5

socket = %(base)/%(project)/%(project).sock
chmod-socket = 664
vacuum = true
```
```
$ sudo nano /etc/init/uwsgi.conf
```
```
description "uWSGI application server"

start on runlevel [2345]
stop on runlevel [!2345]

setuid user
setgid www-data

exec /usr/local/bin/uwsgi --emperor /etc/uwsgi/sites
```

#### Gunicorn
```
$ sudo nano /etc/init/gunicorn.conf
$ sudo service gunicorn start
$ sudo service gunicorn stop
$ sudo service gunicorn restart
```

#### uWSGI
```
$ sudo nano /etc/init/uwsgi.conf
$ sudo service uwsgi start
$ sudo service uwsgi stop
$ sudo service uwsgi restart
```
