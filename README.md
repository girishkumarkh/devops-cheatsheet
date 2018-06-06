# DevOps Ubuntu Cheat Sheet

#### Initial ubuntu 14.04
```
$ sudo apt-get update
$ sudo apt-get upgrade
$ sudo apt-get install python-psycopg2
$ sudo apt-get install libpq-dev
$ sudo apt-get install libjpeg-dev
```

#### Initial ubuntu 16.04
```
$ sudo apt-get update && sudo apt-get -y upgrade
$ sudo apt-get install software-properties-common python-software-properties
$ sudo add-apt-repository ppa:jonathonf/python-3.6
$ sudo apt-get update
$ sudo apt-get install python3.6

# get pip
$ cd /home
$ curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
$ python3.6 get-pip.py
$ pip install virtualenv
```

#### List of comands
```
# Show list of installed apps
$ apt list --installed
# Filter a package 
$ apt list --installed | grep <package name> 

# Remove old python
$ sudo apt-get remove python3.5
$ sudo apt-get purge python3.5
# do the same for all the packges
# Check Python
$ whereis python3.5

# Fix UTF-8 Issue
$ export LC_ALL="en_US.UTF-8"
$ export LC_CTYPE="en_US.UTF-8"
$ sudo dpkg-reconfigure locales
```

#### Permision settings
```
$ sudo groupadd wwwusers
$ sudo adduser www-data wwwusers
$ sudo chgrp -R wwwusers /home/<project-name>/public/
$ sudo chmod -R 777 /home/<project-name>/public/
```

#### SSH config
```
$ sudo nano /etc/ssh/sshd_config
# Add more SSH keys
$ cat  ~/.ssh/authorized_keys
$ sudo nano ~/.ssh/authorized_keys
```

#### MOTD (Message of the day)
```
$ cat /etc/motd.tail
$ sudo nano /etc/motd.tail
# text generator - http://www.kammerl.de/ascii/AsciiSignature.php
$ ls /etc/update-motd.d/
$ sudo nano /etc/update-motd.d/99-footer
$ sudo chmod -R 755 /etc/update-motd.d/99-footer
```
`/etc/update-motd.d/99-footer`
```
#!/bin/sh

[ -f /etc/motd.tail ] && cat /etc/motd.tail || true
```

#### Activate venv at SSH
```
$ cat ~/.bashrc
$ sudo nano ~/.bashrc
```
`~/.bashrc` last line
```
# Activate Venv
source /home/<path>/venv/bin/activate
cd /home
```

#### Generating SSH Keys
```
$ ssh-keygen -t rsa -b 4096
# Enter file in which to save the key (/home/ubuntu/.ssh/id_rsa):
# Enter passphrase (empty for no passphrase):
$ cat ~/.ssh/id_rsa.pub

# start the ssh-agent in the background if it's not already running
$ eval "$(ssh-agent -s)"
Agent pid 59566

$ ssh-add ~/.ssh/id_rsa
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

#### git
```
# Remove local git tags
$ git tag -d TagName && git push origin :refs/tags/TagName
```

## Postgresql

### Install PostgreSQL 10 on Ubuntu [Link to orginal gist](https://gist.github.com/alistairewj/8aaea0261fe4015333ddf8bed5fe91f8)

This is a quick guide to install PostgreSQL 10 - tested on Ubuntu 16.04 but likely can be used for Ubuntu 14.04 and 17.04 as well, with one minor modification detailed below.

#### (Optional) Uninstall other versions of postgres

To make life simple, remove all other versions of Postgres. Obviously not required, but again, makes life simple.

```
dpkg -l | grep postgres
```

returned for me:

```
ii  postgresql                                  9.5+173                                                     all          object-relational SQL database (supported version)
ii  postgresql-9.5                              9.5.8-0ubuntu0.16.04.1                                      amd64        object-relational SQL database, version 9.5 server
ii  postgresql-client-9.5                       9.5.8-0ubuntu0.16.04.1                                      amd64        front-end programs for PostgreSQL 9.5
ii  postgresql-client-common                    173                                                         all          manager for multiple PostgreSQL client versions
ii  postgresql-common                           173                                                         all          PostgreSQL database-cluster manager
ii  postgresql-contrib-9.5                      9.5.8-0ubuntu0.16.04.1                                      amd64        additional facilities for PostgreSQL
```

... therefore I ran:

```sh
sudo apt-get --purge remove postgresql postgresql-9.5 postgresql-client-9.5 postgresql-client-common  postgresql-common postgresql-contrib-9.5
```


#### Add PostgreSQL apt repository

The current default Ubuntu apt repositories only have up to postgresql-9.6. To get 10, we'll add the official postgres apt repository.

* Ubuntu 14.04: `sudo add-apt-repository 'deb http://apt.postgresql.org/pub/repos/apt/ trusty-pgdg main'`
* Ubuntu 16.04: `sudo add-apt-repository 'deb http://apt.postgresql.org/pub/repos/apt/ xenial-pgdg main'`
* Ubuntu 17.04: `sudo add-apt-repository 'deb http://apt.postgresql.org/pub/repos/apt/ zesty-pgdg main'`

Now import the repository signing key, followed by an update to the package lists:

```
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | \
  sudo apt-key add -
sudo apt-get update
```

#### Install PostgreSQL

```sh
sudo apt-get install postgresql-10
```

Ensure that the server is started by switching to the postgres user.

```sh
sudo su - postgres
/usr/lib/postgresql/10/bin/pg_ctl -D /var/lib/postgresql/10/main -l logfile start
```

If that fails, the server might be running, so restart it to be safe: `/usr/lib/postgresql/10/bin/pg_ctl -D /var/lib/postgresql/10/main -l logfile restart`

That should return something like:

```sh
postgres@computer-name:~$ /usr/lib/postgresql/10/bin/pg_ctl -D /var/lib/postgresql/10/main -l logfile restart
waiting for server to shut down.... done
server stopped
waiting for server to start.... done
server started
```

Which means your PostgreSQL 10 is up and running!

#### (Optional) Create a user for yourself

Since we're still logged in as the postgres user, now is a good time to create your own user account.
This allows you to use operating system level authentication locally, greatly simplifying access to the database. I also like to create a database with my username, as this is the default database that `psql` will connect to, and if the database doesn't exist `psql` throws a pesky error (which gets me every time).

```
psql
CREATE ROLE <username> SUPERUSER LOGIN REPLICATION CREATEDB CREATEROLE;
CREATE DATABASE <username> OWNER <username>;
\q
```

Now we can exit the postgres user and query normally!

```sh
postgres@computer:~$ logout
```

#### Core PostgreSQL
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

#### Updating PostgreSQL 9.3 to 9.4 on Ubuntu 14
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

### MongoDB
```
$ killall -15 mongod
```
