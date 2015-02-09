# server config
GlobAllomeTree CentOS 6.5 Server configuration

Please make any changes to the repository at https://github.com/GlobAllomeTree/config rather than changing these files directly

```
-------------------------------- Install Log ----------------------------------
yum install git

# Clone the server config repository into globallometree_config
git clone https://github.com/GlobAllomeTree/config globallometree_config

# Clone the app repository into globallometree_app
git clone https://github.com/GlobAllomeTree/GlobAllomeTree globallometree_app


#################### YUM REPOS ########################

yum install epel-release
ln -s /opt/globallometree_config/nginx/nginx.repo /etc/yum.repos.d/nginx.repo
rpm --import https://packages.elasticsearch.org/GPG-KEY-elasticsearch
ln -s /opt/globallometree_config/elasticsearch/elasticsearch.repo /etc/yum.repos.d/elasticsearch.repo

#################### PYTHON GENERAL ######################

# PIP and python devel headers
yum install python-pip python-devel python-virtualenv
# For PDF creation
yum install libxslt-devel libxml2-devel python-lxml
# For Python PIL
yum install libtiff-devel libjpeg-devel libzip-devel freetype-devel lcms2-devel tcl-devel tk-devel gcc


#################### POSTGRESQL ######################

# Change the default data directory for postgresql
mkdir -p /opt/globallometree_data/postgresql

# Let the postgresql-9.4 service know that we changed the default data directory
mkdir -p /etc/sysconfig/pgsql
echo "PGDATA=/opt/globallometree_data/postgresql" >> /etc/sysconfig/pgsql/postgresql-9.4

rpm -Uvh http://yum.postgresql.org/9.4/redhat/rhel-6-x86_64/pgdg-centos94-9.4-1.noarch.rpm
yum update
yum install postgresql94-server postgresql94-contrib postgresql94-devel postgresql94-python python-psycopg2
chown postgres /opt/globallometree_data/postgresql

# Initialize the database in the new directory
service postgresql-9.4 initdb

# Symlink to the config in the globallometree_config repository
rm /opt/globallometree_data/postgresql/pg_hba.conf
chown postgres /opt/globallometree_config/postgresql/pg_hba.conf
ln -s /opt/globallometree_config/postgresql/pg_hba.conf /opt/globallometree_data/postgresql/pg_hba.conf

# Indicate that the postgresql service will run by default
chkconfig postgresql-9.4 on

# Start the postgresql service
service postgresql-9.4 start

# Create the database user and globallometree database
# Switch to the postgres user for management
sudo su postgres

# Use the psql shell
psql

# In the psql shell, create the user + db
postgres=# CREATE USER globallometree WITH PASSWORD '***';
postgres=# CREATE DATABASE globallometree OWNER globallometree;
postgres=# \q

# Switch back to the root user from the postgres user
exit

# Import the database dump (which was uploaded via scp)
psql -U globallometree globallometree < /opt/globallometree_data/initial/globallometree.normalized.2015.02.1.sql 


###################### GLOBALLOMETREE PYTHON PACKAGES ########################

# Create a virtual environment 
# We include system site packages to take advantage of lxml and psycopg2
virtualenv --system-site-packages /opt/globallometree_virtualenv

# Activate the virtual environment
source /opt/globallometree_virtualenv/bin/activate

# Use pip to install the requirements
pip install -r /opt/globallometree_app/server/requirements.txt

# Deactivate the virtual environment
deactivate

################### SUPERVISOR #############################

# Install via yum
yum install supervisor

# Make sure the service is on by default
chkconfig supervisord on

# Link to our config file
rm /etc/supervisord.conf
ln -s /opt/globallometree_config/supervisor/supervisord.conf /etc/supervisord.conf
service supervisord start

#################### NGINX ###################

# Install nginx package
yum install nginx
chkconfig nginx on

# Remove default nginx server
rm /etc/nginx/conf.d/default.conf
rm /etc/nginx/conf.d/example_ssl.conf

# Symlink the config
ln -s /opt/globallometree_config/nginx/globallometree.conf /etc/nginx/conf.d/globallometree.conf

# Start nginx
service nginx start

################## ELASTICSEARCH #####################

# Install via yum
yum install elasticsearch
chkconfig --add elasticsearch

# Link our configuration
rm /etc/elasticsearch/elasticsearch.yml
ln -s /opt/globallometree_config/elasticsearch/elasticsearch.yml /etc/elasticsearch/elasticsearch.yml

# Create our data directory
mkdir -p /opt/globallometree_data/elasticsearch
chown elasticsearch.elasticsearch /opt/globallometree_data/elasticsearch

# Start elasticsearch
service elasticsearch start

################ GLOBALLOMETREE APPLICATION ####################

# Create the static and media directories
mkdir -p /opt/globallometree_data/web/media/
mkdir -p /opt/globallometree_data/web/static/

# Collect the static media
source /opt/globallometree_virtualenv/bin/activate
/opt/globallometree_/manage.py collectstatic

# Rebuild the allometric equation index
/opt/globallometree_/manage.py rebuild_equation_index

```
