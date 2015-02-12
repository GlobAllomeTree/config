
Steps for creating the phase 2 database from the phase 1 database

```
sudo -u postgres psql
DROP DATABASE globallometree;
CREATE DATABASE globallometree OWNER globallometree;

psql -U globallometree < globallometreedb.phase_1_db.sql

./manage.py dbshell
SELECT * INTO linkbox_linkbox FROM cmsplugin_linkbox;
DROP table cmsplugin_linkbox;
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
./manage.py normalize
./manage.py geocode_accounts

```

-- Go to admin --
/admin/cms/page/
Publish the home page
Publish all the other pages

-- Edit homepage --
Change the allometric equation database box link

-- Edit database page --
Remove the geomap
Edit the link for the Equations box to be /allometric-equations/search/
Edit the link for the submit equations box to be /allometric-equations/submit
