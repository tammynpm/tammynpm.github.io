---
title: "Mediawiki 1.41"
date: 2026-08-10T10:54:00-04:00
draft: true
---

# migrating from Mediawiki1.39 to Mediawiki1.41

This is a workaround to upgrade MW 1.39 to 1.43. 

Background information: 

OS: AlmaLinux 10

after installing Docker, add user to the docker group `sudo usermod -aG docker $USER` then `newgrp docker` to apply the change. 

create `.env` and `docker-compose.override.yml`. `.env` needs the docker group id. You can find it with `getent group docker`

sample `.env`:

```
MW_DOCKER_UID=1000 
MW_DOCKER_GID=994
XDEBUG_CONFIG=
```

`docker compose up -d`



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

2. Create `/var/lib/mysql/` directory 
3. Spin up the database container 
4. import the dump into the database container: 
```
docker compose exec -T db mariadb -u root -prootpass dbname < dump.sql
```

5. Run schema updater

```
docker compose exec mediawiki php maintenance/run.php update
```

Change the database settings in `LocalSettings.php` as well. 



Revision -> slot -> content -> text 

```
[mw@mediawiki1 mediawiki-1.41.5]$ docker compose exec mediawiki php maintenance/update.php --quick
WARN[0000] /home/mw/mediawiki-1.41.5/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion 

*******************************************************************************
NOTE: Do not run maintenance scripts directly, use maintenance/run.php instead!
      Running scripts directly has been deprecated in MediaWiki 1.40.
      It may not work for some (or any) scripts in the future.
*******************************************************************************

MediaWiki 1.41.5 Updater

Your composer.lock file is up to date with current dependencies!
Going to run database updates for cswiki
Depending on the size of your database this may take a while!
...collations up-to-date.
...have rev_actor field in revision table.
...watchlist_expiry table already exists.
...page_restrictions field does not exist in page table, skipping modify field patch.
...index ipb_address_unique already set on ipblocks table.
...archive table does not contain ar_text_id field.
...lc_lang is up-to-date.
...ll_lang is up-to-date.
...site_language is up-to-date.
...index ipb_address_unique on table ipblocks has no field ipb_anon_only; added.
...ipb_address_unique index up-to-date.
...actor_name in table actor already modified by patch patch-actor-actor_name-varbinary.sql.
...site_global_key in table sites already modified by patch patch-sites-site_global_key.sql.
...iwl_prefix in table iwlinks already modified by patch patch-extend-iwlinks-iwl_prefix.sql.
...rd_title in table redirect already modified by patch patch-redirect-rd_title-varbinary.sql.
...pl_title in table pagelinks already modified by patch patch-pagelinks-pl_title-varbinary.sql.
...tl_title field does not exist in templatelinks table, skipping modify field patch.
...il_to in table imagelinks already modified by patch patch-imagelinks-il_to-varbinary.sql.
...ll_title in table langlinks already modified by patch patch-langlinks-ll_title-varbinary.sql.
...iwl_title in table iwlinks already modified by patch patch-iwlinks-iwl_title-varbinary.sql.
...cat_title in table category already modified by patch patch-category-cat_title-varbinary.sql.
...qc_title in table querycache already modified by patch patch-querycache-qc_title-varbinary.sql.
...qcc_title in table querycachetwo already modified by patch patch-querycachetwo-qcc_title-varbinary.sql.
...wl_title in table watchlist already modified by patch patch-watchlist-wl_title-varbinary.sql.
...user_last_timestamp in table user_newtalk already modified by patch patch-user_newtalk-user_last_timestamp-binary.sql.
...pt_title in table protected_titles already modified by patch patch-protected_titles-pt_title-varbinary.sql.
...ir_type in table ipblocks_restrictions already modified by patch patch-ipblocks_restrictions-ir_type.sql.
...index wl_namespace_title already set on watchlist table.
...job_title in table job already modified by patch patch-job-job_title-varbinary.sql.
...job_timestamp in table job already modified by patch patch-job_job_timestamp.sql.
...job_token_timestamp in table job already modified by patch patch-job_job_token_timestamp.sql.
...wl_notificationtimestamp in table watchlist already modified by patch patch-watchlist-wl_notificationtimestamp.sql.
...role_id in table slot_roles already modified by patch patch-slot_roles-role_id.sql.
...model_id in table content_models already modified by patch patch-content_models-model_id.sql.
...cl_to in table categorylinks already modified by patch patch-categorylinks-cl_to-varbinary.sql.
...log_title in table logging already modified by patch patch-logging-log_title-varbinary.sql.
...us_timestamp in table uploadstash already modified by patch patch-uploadstash-us_timestamp.sql.
...index up_property already set on user_properties table.
...index site_global_key already set on sites table.
...index log_type_time already set on logging table.
...fa_name in table filearchive already modified by patch patch-filearchive-fa_name.sql.
...oi_name in table oldimage already modified by patch patch-oldimage-oi_name-varbinary.sql.
...exptime in table objectcache already modified by patch patch-objectcache-exptime-notnull.sql.
...index ar_name_title_timestamp already set on archive table.
...img_name in table image already modified by patch patch-image-img_name-varbinary.sql.
...img_timestamp in table image already modified by patch patch-image-img_timestamp.sql.
...index si_key already set on site_identifiers table.
...rc_title in table recentchanges already modified by patch patch-recentchanges-rc_title-varbinary.sql.
...rc_timestamp in table recentchanges already modified by patch patch-recentchanges-rc_timestamp.sql.
...rc_id in table recentchanges already modified by patch patch-recentchanges-rc_id.sql.
...index rc_new_name_timestamp already set on recentchanges table.
...ar_title in table archive already modified by patch patch-archive-ar_title-varbinary.sql.
...page_title in table page already modified by patch patch-page-page_title-varbinary.sql.
...user_name in table user already modified by patch patch-user_table-updates.sql.
...index rev_page_timestamp already set on revision table.
...have modtoken field in objectcache table.
...index oi_timestamp already set on oldimage table.
...index page_name_title already set on page table.
...index ct_rc_tag_id already set on change_tag table.
...page_restrictions table does not contain pr_user field.
...fa_id in table filearchive already modified by patch patch-filearchive-fa_id.sql.
...img_major_mime in table image already modified by patch patch-image-img_major_mime-default.sql.
...linktarget table already exists.
...rev_page_id key doesn't exist.
...pr_page in table page_restrictions already modified by patch patch-page_restrictions-pr_page.sql.
...pp_page in table page_props already modified by patch patch-page_props-pp_page.sql.
...ir_value in table ipblocks_restrictions already modified by patch patch-ipblocks_restrictions-ir_value.sql.
...have tl_target_id field in templatelinks table.
...user_autocreate_serial table already exists.
...ir_ipb_id in table ipblocks_restrictions already modified by patch patch-ipblocks_restrictions-ir_ipb_id.sql.
...ipb_id in table ipblocks already modified by patch patch-ipblocks-ipb_id.sql.
...user_editcount in table user already modified by patch patch-user-user_editcount.sql.
Running maintenance/migrateRevisionActorTemp.php...
...Update 'MigrateRevisionActorTemp' already logged as completed. Use --force to run it again.
done.
...revision_actor_temp doesn't exist.
Running maintenance/updateRestrictions.php...
Migration is not needed.
done.
...page table does not contain page_restrictions field.
...templatelinks table has already been migrated.
...tl_namespace field does not exist in templatelinks table, skipping modify field patch.
...templatelinks table does not contain tl_title field.
Adding el_to_path field to table externallinks...done.
Adding user_is_temp field to table user...done.
Running maintenance/migrateRevisionCommentTemp.php...
Merging the revision_comment_temp table into the revision table...
... rev_id=263, updated 200
... rev_id=517, updated 400
... rev_id=735, updated 600
... rev_id=938, updated 800
... rev_id=1145, updated 1000
... rev_id=1389, updated 1200
... rev_id=1594, updated 1400
... rev_id=1818, updated 1600
... rev_id=2020, updated 1800
... rev_id=2221, updated 2000
... rev_id=2421, updated 2200
... rev_id=2662, updated 2400
... rev_id=2886, updated 2600
... rev_id=3098, updated 2800
... rev_id=3301, updated 3000
... rev_id=3506, updated 3200
... rev_id=3706, updated 3400
... rev_id=3906, updated 3600
... rev_id=4114, updated 3800
Completed merge of revision_comment_temp into the revision table, 3879 rows updated.
done.
Dropping table revision_comment_temp ...done.
Running maintenance/migrateExternallinks.php...
Populating el_to_domain_index and el_to_path columns
Updated 602 rows
PHP Warning:  Undefined array key "path" in /var/www/html/w/includes/ExternalLinks/LinkFilter.php on line 196
PHP Deprecated:  explode(): Passing null to parameter #2 ($string) of type string is deprecated in /var/www/html/w/includes/ExternalLinks/LinkFilter.php on line 203
PHP Warning:  Undefined array key "path" in /var/www/html/w/includes/ExternalLinks/LinkFilter.php on line 196
PHP Deprecated:  explode(): Passing null to parameter #2 ($string) of type string is deprecated in /var/www/html/w/includes/ExternalLinks/LinkFilter.php on line 203
Updated 831 rows
Updated 310 rows
Completed normalization of externallinks, 1743 rows updated.
done.
Modifying el_to field of table externallinks...done.
Adding pl_target_id field to table pagelinks...done.
Table externallinks contains el_to field. Dropping...done.
Running maintenance/fixInconsistentRedirects.php...
Fixing inconsistent redirects ...
Estimated redirect page count: 15
0/15
Done, updated 0 of 15 rows.
done.
Modifying img_size field of table image...done.
Modifying fa_size field of table filearchive...done.
Modifying oi_size field of table oldimage...done.
Modifying us_size field of table uploadstash...done.
...site_stats is populated...done.
...Update 'populate rev_len and ar_len' already logged as completed. Use --force to run it again.
...Update 'populate rev_sha1' already logged as completed. Use --force to run it again.
...img_sha1 column of image table already populated.
...fa_sha1 column of filearchive table already populated.
...*_from_namespace column of backlink tables already populated.
...Update 'FixDefaultJsonContentPages' already logged as completed. Use --force to run it again.
...Update 'cleanup empty categories' already logged as completed. Use --force to run it again.
...RFC and PMID already added to interwiki database table.
...Update 'populate pp_sortkey' already logged as completed. Use --force to run it again.
...Update 'populate ip_changes' already logged as completed. Use --force to run it again.
Purging caches...done.
```


Define the namespaces in `LocalSettings.php`

Now that the database is correctly imported, we can dump it. 

```
docker compose exec db mariadb-dump -u root -prootpass --single-transaction dbname > mw_1415_export.sql
```


NOTE: NOTE: Do not run maintenance scripts directly, use maintenance/run.php instead!
      Running scripts directly has been deprecated in MediaWiki 1.40.
      It may not work for some (or any) scripts in the future.



Very important Note: 

`LocalSettings.php` gets created by running the installer `install.sh` or teh `mw-config` installer. 

Since we have a populated database before running the installer, MediaWiki won't auto-generate `LocalSettings.php`. Instead, you have to manually create it. 


