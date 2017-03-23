Steps involved in putting django project into production:

(These steps assume that the Python3 and pip3 has been already installed on the server)

If Python3 and pip3 not installed, do:

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

