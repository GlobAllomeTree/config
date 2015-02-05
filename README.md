# server config
GlobAllomeTree CentOS Server configuration

Please make any changes to the repository at https://github.com/GlobAllomeTree/config rather than changing these files directly

```
-------------------------------- Install Log ----------------------------------
yum install git

# Clone the server config repository into globallometree_config
git clone https://github.com/GlobAllomeTree/config globallometree_config

# Clone the app repository into globallometree_app
git clone https://github.com/GlobAllomeTree/GlobAllomeTree globallometree_app


#################### EPEL ########################

yum install epel-release

#################### PYTHON GENERAL ######################

# PIP and python devel headers
yum install python-pip python-devel python-virtualenv
# For PDF creation
yum install libxslt-devel libxml2-devel python-lxml
# For Python PIL
yum install libtiff-devel libjpeg-devel libzip-devel freetype-devel lcms2-devel tcl-devel tk-devel


#################### POSTGRESQL ######################

# Change the default data directory for postgresql
mkdir /opt/globallometree_data/postgresql
chown postgres /opt/globallometree_data/postgresql

# Let the postgresql-9.4 service know that we changed the default data directory
mkdir -p /etc/sysconfig/pgsql
echo "PGDATA=/opt/globallometree_data/postgresql" >> /etc/sysconfig/pgsql/postgresql-9.4

rpm -Uvh http://yum.postgresql.org/9.4/redhat/rhel-6-x86_64/pgdg-centos94-9.4-1.noarch.rpm
yum update
yum install postgresql94-server postgresql94-contrib postgresql94-devel postgresql94-python python-psycopg2

# Initialize the database in the new directory
service postgresql-9.4 initdb

#Symlink to the config in the globallometree_config repository
rm /opt/globallometree_data/postgresql/pg_hba.conf
chown postgres /opt/globallometree_config/postgresql/pg_hba.conf
ln -s /opt/globallometree_config/postgresql/pg_hba.conf /opt/globallometree_data/postgresql/pg_hba.conf

# Indicate that the postgresql service will run by default
chkconfig postgresql-9.4 on

# Start the postgresql service
service postgresql-9.4 start

```
