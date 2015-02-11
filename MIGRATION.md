
Steps for creating a new database

```
sudo -u postgres psql
DROP DATABASE globallometree;
CREATE DATABASE globallometree OWNER globallometree;

cd ~/ops/dev/globallometree/
psql -U globallometree < globallometreedb.2014.05.26.sql

./manage.py migrate djangocms_text_ckeditor 0001 --fake --delete-ghost-migrations
./manage.py migrate cms --noinput
./manage.py migrate djangocms_link --noinput
./manage.py migrate djangocms_file --noinput
./manage.py migrate menus --noinput
./manage.py migrate data --noinput
./manage.py migrate djangocms_text_ckeditor --noinput
./manage.py migrate django_extensions --noinput
./manage.py syncdb --all --noinput
./manage.py migrate accounts --noinput
./manage.py import_countries
./manage.py import_biomes

```
