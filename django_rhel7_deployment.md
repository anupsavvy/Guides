Steps involved in deploying Django app with Nginx reverse proxy on RHEL7:

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

'''

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

'''
