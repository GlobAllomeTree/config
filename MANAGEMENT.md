
```
source /opt/globallometree_virtualenv/bin/activate


#Rebuild the elasticsearch indexes
/opt/globallometree_app/manage.py rebuild_equation_index
/opt/globallometree_app/manage.py rebuild_userprofile_index

# Collect static media 
/opt/globallometree_app/manage.py collectstatic

```
