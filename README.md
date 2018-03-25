# udacity_linux_project
This project was to configure a secure Ubuntu server, and host an application on it. Therefore, the catalog_application I've created in a previous Udacity project was chosen. In order to run it, the server needs an Apache Webserver, a Postgresql database and some libraries installed. Actually the thoughest part was getting used to Postgresql and adapting the application to use that, as it was initially build with sqlite in mind. 

## Usage
- url: www.fullstacky.com
- ip: 35.178.50.13
- ssh: port 2200

## Configuration
Infrastructure
- Provisioned an Ubuntu server using AWS Lightsail
- Changed SSH port to 2200 (using both AWS Lightsail networking, and Uncomplicated Firewall)
- Allowed inbound access for port 123 and port 80
- Allowed all outbound traffic
- Created Lightsail hosted zone
- Created ALIAS recored (www.fullstacky.com)

Users
- Added `grader` user with sudo permissions
- Added `postgres` user
- Added `catalog` user (also to postgres)

Middleware 
- Installed Apache Web server
- Installed Postgresql
- Installed Flask
- Installed Oauth2client
- Installed requests
- Installed SQLalchemy
- Installed flask-sqlalchemy
- Installed psycopg2

Apache  
- Cloned catalog_application to `/var/www/html/`
- Created `/var/www/html/myapp.wsgi` to forward requests of port 80 to the catalog application (see below)
- Added configuration to `/etc/apache2/sites-available/000-default.conf` (see below)
- Denied access to the `.git` folder from within `/etc/apache2/apache2.conf`

myapp.wsgi
```
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/html/")

from catalog_application.views import app as application
```

000-default (added lines only)
```
    	<Directory /var/www/catalog_application>
        	WSGIProcessGroup catalog_application
        	WSGIApplicationGroup %{GLOBAL}
        	Order deny,allow
        	Allow from all
    	</Directory>
      WSGIScriptAlias / /var/www/html/myapp.wsgi
```

apache2.conf (added lines only)
```
<Directory /var/www/html/catalog_application/.git/>
        Options Includes Indexes FollowSymLinks MultiViews
        AllowOverride None
        Require all denied
</Directory>
```

Postgresql
- Created `catalog` user with password
- Created `catalog` database

Application
- Made changes to database engine uri
- Made changes within Facebook developers account to allow traffic from www.fullstacky.com
- Made changes such that the secrets is invoked globally instead of within a function
- In order to fix an error `No such file or directory: 'fb_client_secrets.json'`, changed the actions to use absolute path

Third party
- Facebook oath

## Resources
Used the following resources to build this task.
- http://modwsgi.readthedocs.io/en/develop/user-guides/quick-configuration-guide.html
- https://blog.theodo.fr/2017/03/developping-a-flask-web-app-with-a-postresql-database-making-all-the-possible-errors/

