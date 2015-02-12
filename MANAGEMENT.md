
```
source /opt/globallometree_virtualenv/bin/activate

cd /opt/globallometee_app/

#Rebuild the elasticsearch indexes
./manage.py rebuild_equation_index
./manage.py rebuild_userprofile_index

```
