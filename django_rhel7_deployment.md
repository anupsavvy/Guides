Steps involved in deploying Django app with Nginx reverse proxy on RHEL7:
```

################# if needed ##############

$ sudo yum -y groupinstall development

$ wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

$ sudo rpm -Uvh epel-release-latest-7*.rp

$ wget https://rhel7.iuscommunity.org/ius-release.rpm

$ sudo rpm -Uvh ius-release*.rpm

################## if needed ##########################

$ sudo yum install python34

$ sudo yum -y install python34u-pip

$ sudo pip3.4 install virtualenv

$ sudo pip3.4 install virtualenvwrapper

$ alias python=/usr/bin/python3

########## Terminal Commands ###########

$ sudo yum install git

$ sudo yum install redis

$ sudo yum install nginx

$ sudo yum install mariadb mariadb-server

$ sudo yum install mysql mysql-devel mysql-lib

########## Edit .bashrc ##############

Following goes in .bashrc under /home/username

$ echo "export WORKON_HOME=~/Env" >> ~/.bashrc

$ echo "source /usr/bin/virtualenvwrapper.sh" >> ~/.bashrc

############ Terminal Commands ###########

$ source ~/.bashrc

$ cd ~/Env

$ virtualenv your_project_name

$ source your_project_name/bin/activate

############## Virtual Env Start ######################################

(mdt) $ cd ~

(mdt) $ git clone path_to_git_repository

################# Folder structure #####################################

Assuming you have similar folder structure when you clone

REPO FOLDER
├── db.sqlite3
├── mdt
│   ├── __init__.py
│   ├── __pycache__
│   │   ├── __init__.cpython-34.pyc
│   │   ├── settings.cpython-34.pyc
│   │   ├── urls.cpython-34.pyc
│   │   └── wsgi.cpython-34.pyc
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── manage.py
└── static
    └── admin
        ├── css
        │   ├── base.css
        │   ├── changelists.css
        
###################### Terminal Commands ################################

(you are still under /home/(your_username))

(mdt) $ mv REPO FOLDER src
(mdt) $ mkdir your_project_name && mv src your_project_name
(mdt) $ cd your_project_name

(Assuming your static root is one level above project base i.e. src)

(mdt) $ mkdir static_in_env

(create other folders if you want to have one level above project base)

(mdt) $ deactivate 

############## Virtual Env End ######################################

################ Current Folder Structure ##########################

mdt
├── mdt.log
├── mdt_request.log
├── networks
├── requirements.txt
├── src
│   ├── accounts
│   │   ├── admin.py
│   │   ├── apps.py
│   │   ├── forms.py
│   │   ├── __init__.py
│   │   ├── migrations
│   │   │   ├── 0001_initial.py
│   │   │   ├── __init_

################## Terminal Commands ###################################

$ sudo yum install python34-devel gcc
$ sudo pip3.4 install uwsgi
$ sudo mkdir -p /etc/uwsgi/sites
$ cd /etc/uwsgi/sites
$ sudo vi your_project_name.ini

################## Put following in your_project_name.ini ###############

[uwsgi]
project = YOUR_PROJECT_NAME
username = USERNAME
base = /home/%(username)

chdir = %(base)/%(project)/src
home = /home/%(username)/Env/YOUR_PROJECT_NAME
module = %(project).wsgi:application

master = true
processes = 5

uid = %(username)
socket = /run/uwsgi/%(project).sock
chown-socket = %(username):nginx
chmod-socket = 660
vacuum = true

######################## Save and Close file (escape :wq) #################################


$ sudo vi /etc/systemd/system/uwsgi.service


################## Put following in uwsgi.service ###############

[Unit]
Description=uWSGI Emperor service

[Service]
ExecStartPre=/usr/bin/bash -c 'mkdir -p /run/uwsgi; chown USERNAME:nginx /run/uwsgi'
ExecStart=/usr/bin/uwsgi --emperor /etc/uwsgi/sites
Restart=always
KillSignal=SIGQUIT
Type=notify
NotifyAccess=all

[Install]
WantedBy=multi-user.target

######################## Save and Close file (escape :wq) #################################

$ sudo vi /etc/nginx/nginx.conf


################## Put following server block in nginx.conf ###############

server {
        listen 80;
        server_name {{domain}};

        location = favicon.ico { access_log off; log_not_found off;}
        location /static/ {
                alias /home/ass198/mdt/(YOUR STATIC ROOT FOLDER);
        }
        location /media/ {
                alias /home/ass198/mdt/(YOUR MEDIA ROOT FOLDER);
        }

        location / {
                include uwsgi_params;
                uwsgi_pass unix:/run/uwsgi/YOUR_PROJECT_NAME.sock;
        }

    }

######################## Save and Close file (escape :wq) #################################

(check if nginx conf file is correct)

$ sudo nginx -t

(If no errors are reported, our file is in good condition.)


$ sudo usermod -a -G USERNAME nginx
$ chmod 710 /home/USERNAME

$ cd ~/YOUR_PROJECT_NAME
$ cd src/YOUR_PROJECT_NAME/

########################## OPEN settings.py #######################

DEBUG = False

ALLOWED_HOSTS = ["domain"]

######################## Save and Close file (escape :wq) #################################

$ sudo systemctl start redis.service
$ sudo systemctl start mariadb.service

(Assuming you have already configured django settings.py to connect to Mysql db)

$ cd ..
$ workon YOUR_PROJECT_NAME

( you are in your virtual env now)

$ python manage.py makemigrations
$ python manage.py migrate
$ deactivate

( you are out of virtual env now)
$ sudo fuser -k 80/tcp
$ sudo systemctl start nginx
$ sudo systemctl start uwsgi

######################## Your application is up and running #################################

################## Getting SSL certificate ##################################################

1) Open optional channel for RHEL-7

2) $ sudo yum install python-zope-interface

3) $ sudo yum install certbot

4) Add following to /etc/nginx/nginx.conf inside the server block

    location /.well-known {
        alias /var/www/{{site_name}}/.well-known;
    }
    
5) $ cd /var/www

6) $ sudo mkdir {{site_name}}

7) $ cd {{site_name}}

8) $ sudo mkdir .well-known

9) $ sudo systemctl restart nginx

10) $ sudo certbot certonly -a webroot --webroot-path=/var/www/{{site_name}} -d {{site_name}}

11) $ sudo openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048

12) Add following to /etc/nginx/nginx.conf inside the server block

    ssl_session_timeout 1d;
    ssl_session_cache shared:SSL:50m;
    ssl_session_tickets off;

    ssl_dhparam /etc/ssl/certs/dhparam.pem;

    ssl_certificate /etc/letsencrypt/live/{{ site_name }}/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/{{ site_name }}/privkey.pem;
    
    ssl_ciphers 'ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:ECDHE-RSA-DES-CBC3-SHA:ECDHE-ECDSA-DES-CBC3-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:AES:CAMELLIA:DES-CBC3-SHA:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!MD5:!PSK:!aECDH:!EDH-DSS-DES-CBC3-SHA:!EDH-RSA-DES-CBC3-SHA:!KRB5-DES-CBC3-SHA';
    ssl_prefer_server_ciphers on;

    add_header Strict-Transport-Security max-age=15768000;

    ssl_stapling on;
    ssl_stapling_verify on;

13) sudo crontab -e

14) Add following to the file that opens and save

    30 2 * * 1 /usr/bin/certbot renew >> /var/log/le-renew.log
    35 2 * * 1 /usr/bin/systemctl reload nginx
    
15) sudo systemctl enable nginx

16) sudo systemctl enable uwsgi

```
