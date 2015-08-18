
Steps for creating the phase 2 database from the phase 1 database

```
sudo -u postgres psql
DROP DATABASE globallometree;
CREATE DATABASE globallometree OWNER globallometree;

Initialize a new database
./manage.py migrate 

Restore selected tables from the database dump
pg_restore -U globallometree -d globallometree --data-only -t auth_user globallometreedb-201508180929.backup
pg_restore -U globallometree -d globallometree --data-only -t accounts_userprofile globallometreedb-201508180929.backup
pg_restore -U globallometree -d globallometree --data-only -t cms_plugin globallometreedb-201508180929.backup
pg_restore -U globallometree -d globallometree --data-only -t journals_journal globallometreedb-201508180929.backup
pg_restore -U globallometree -d globallometree --data-only -t journals_article globallometreedb-201508180929.backup

./manage.py import_countries
./manage.py import_biomes


Create a tar of the old media 
tar -czvf /tmp/media.2015.02.11.tar.gz /opt/apps/globallometree/media/
scp to local machine

```

-- Go to admin --
/admin/cms/page/
Publish the home page
Publish all the other pages

Create a user profile for the globallometree user

-- Edit homepage --
Change the allometric equation database box link

-- Edit database page --
Remove the geomap
Edit the link for the Equations box to be /allometric-equations/search/
Edit the link for the submit equations box to be /allometric-equations/submit


On launch

Remove from nginx.conf in the config repository
```
location /robots.txt {
        return 200 "User-agent: *\nDisallow: /";
    }
```
