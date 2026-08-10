---
title: "Mediawiki 1.41"
date: 2026-08-10T10:54:00-04:00
draft: true
---

# migrating from Mediawiki1.39 to Mediawiki1.41

This is a workaround to upgrade MW 1.39 to 1.43. 

Background information: 

OS: AlmaLinux 10

Run the bundled script to install 

```
docker compose exec mediawiki /bin/bash /docker/install.sh
WARN[0000] /home/mw/mediawiki-1.41.5/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion 
+ php maintenance/install.php --server http://localhost:8080 --scriptpath=/w --dbtype sqlite --dbpath /var/www/html/w/cache/sqlite --lang en --pass dockerpass MediaWiki Admin

*******************************************************************************
NOTE: Do not run maintenance scripts directly, use maintenance/run.php instead!
      Running scripts directly has been deprecated in MediaWiki 1.40.
      It may not work for some (or any) scripts in the future.
*******************************************************************************

PHP 8.1.20 is installed.
ICU 65.1 is installed (supports Unicode 12.1.0).
Found ImageMagick: /usr/bin/convert. Image thumbnailing will be enabled if you enable uploads.
Found the Git version control software: /usr/bin/git.
Using server name "http://localhost".
Using server URL "http://localhost:8080/w".
Warning: Your default directory for uploads (/var/www/html/w/images/) is not checked for vulnerability to arbitrary script execution during the CLI install.
Warning: Because of a connection error, it was not possibly to verify that images in your uploads directory, respond with the HTTP header X-Content-Type-Options: nosniff to protect browsers from potentially unsafe files. It is highly recommended to configure appropriate response headers on your webserver before enabling uploads.
The environment has been checked. You can install MediaWiki.
Setting up database
done
Creating tables, step one
done
Creating tables, step two
done
Populating default interwiki table
done
Initializing statistics
done
Generating secret keys
done
Prevent running unneeded updates
done
Restoring MediaWiki services
done
Creating administrator user account
done
Creating main page with default content
done
Database was successfully set up
MediaWiki has been successfully installed. You can now visit <http://localhost:8080/w> to view your wiki. If you have questions, check out our frequently asked questions list: <https://www.mediawiki.org/wiki/Special:MyLanguage/Manual:FAQ> or use one of the support forums linked on that page.
```


the compose file is minimal by design. Because I don't want `localhost`, so I had to put the IP address in the `docker-compose.override.yml`: 

```
services:
  mediawiki:
    environment:
      MW_SERVER: 'http://10.0.0.221:8080'
```

Also if the install.sh script was already ran, we have to chagne the baked-in `$wgServer` value in `LocalSettings.php`.


### importing MediaWiki1.39 Database dump 

1. Update the override file to use the correct database name as from the dump.
```
services:
  db:
    image: mariadb:10.6
    restart: unless-stopped
    environment:
      MARIADB_ROOT_PASSWORD: rootpass
      MARIADB_DATABASE: dbname
      MARIADB_USER: wikiuser
      MARIADB_PASSWORD: wikipass
    volumes:
      - mediawiki-db-data:/var/lib/mysql

  mediawiki:
    depends_on:
      - db
    environment:
      MW_SERVER: 'http://10.0.0.221:8080'
      MW_DBTYPE: 'mysql'
      MW_DBSERVER: 'db'
      MW_DBNAME: 'dbname'
      MW_DBUSER: 'wikiuser'
      MW_DBPASS: 'wikipass'

volumes:
  mediawiki-db-data:
```

2. Spin up the database container 
3. import the dump into the database container: 
```
docker compose exec -T db mariadb -u root -prootpass dbname < dump.sql
```

4. Run schema updater
```
docker compose exec mediawiki php maintenance/update.php --quick
```


```
