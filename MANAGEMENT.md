
```
source /opt/globallometree_virtualenv/bin/activate
cd /opt/globallometree_app

#Rebuild the elasticsearch indexes
./manage.py rebuild_equation_index
./manage.py rebuild_userprofile_index

# Collect static media 
./manage.py collectstatic



# Restart the application
sudo service supervisord restart

```
