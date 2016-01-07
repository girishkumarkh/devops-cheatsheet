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
