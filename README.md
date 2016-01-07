# DevOps Cheat Sheet

#### Ubuntu
```
$ sudo apt-get update
$ sudo apt-get upgrade
$ sudo apt-get install python-psycopg2
$ sudo apt-get install libpq-dev
$ sudo apt-get install libjpeg-dev

# Permision settings
# ------------------
$ sudo groupadd wwwusers
$ sudo adduser www-data wwwusers
$ sudo chgrp -R wwwusers /home/<project-name>/public/
$ sudo chmod -R 777 /home/<project-name>/public/

# Get postgresql dump from a remote location
# ------------------
$ pg_dump --host=<host-name> --dbname=<db-name> --username=<db-username> --port=<port>

# SCP (Secure copy) securely transferring computer files between a local host 
and a remote host or between two remote hosts.
# ------------------
# REMOTE to REMOTE:
$ scp username1@hostname1:/path/to/file username2@hostname2:/path/to/other/file
# REMOTE to LOCAL:
$ scp username@hostname:/path/to/remote/file /path/to/local/file
# LOCAL to REMOTE:
$ scp /path/to/local/file username@hostname:/path/to/remote/file
# Using with port no:
$ scp -P 1234 username@hostname:/path/to/remote/file /path/to/local/file
```

#### PostgreSQL
```
$ sudo su - postgres
$ createdb mydb
$ createuser <myuser> -P
$ psql
$ GRANT ALL PRIVILEGES ON DATABASE mydb TO myuser;
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
```

#### Gunicorn
```
$ sudo nano /etc/init/gunicorn.conf
$ sudo service gunicorn start
$ sudo service gunicorn stop
$ sudo service gunicorn restart
```
