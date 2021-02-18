---
title: Migrate Rancher database from container to external
author: penguin
type: post
date: 2016-05-30T15:01:59+00:00
url: /2016/05/30/rancher-database-migration/
categories:
  - Infrastructure
tags:
  - rancher

---
I wanted to switch from an in-container database setup to an external database setup. And I didn't know what happens when you just lose all database contents, and I thought with Docker and some tweaking that should also not be necessary. So I just migrated the databases. Here's what I did for those interested:

  * stop rancher
  * use a container ([sameersbn/mysql][1]) to mount the rancher database content and do a mysqldump
  * import the dump into the external database (AWS RDS instance)
  * start rancher up with different parameters (use external database, as [described in the official docs][2])

And now the actual command lines:

```
# create socket directory
$ cd RANCHER_MYSQL_MOUNT
$ mkdir sockets

# start sameersbn/mysql to have a mysql container for dumping everything
$ docker run -d --name temp-mysql -v $(pwd)/sockets:/var/run/mysqld -v $(pwd):/var/lib/mysql sameersbn/mysql

# dump the database
$ mysqldump -S ./sockets/mysqld.sock --add-drop-database --add-drop-table --add-drop-trigger --routines --triggers cattle &gt; cattle.sql

# restore the database in AWS / whatever
$ mysql -u USERNAME -p -h DB_HOST DB_NAME &lt; cattle.sq
```

(Don't forget to stop the sammersbn container once you're done). I have configured puppet to start rancher. The final configuration in puppet looks like this:

```
::docker::run { 'rancher-master':
  image   =&gt; 'rancher/server',
  ports   =&gt; "${rancher_port}:8080",
  volumes =&gt; [],
  env     =&gt;  [
    "CATTLE_DB_CATTLE_MYSQL_HOST=${db_host}",
    "CATTLE_DB_CATTLE_MYSQL_NAME=${db_name}",
    "CATTLE_DB_CATTLE_MYSQL_PORT=${db_port}",
    "CATTLE_DB_CATTLE_MYSQL_USERNAME=${db_user}",
    "CATTLE_DB_CATTLE_MYSQL_PASSWORD=${db_pass}",
  ],
}
```

Restart, and it seems to be working just fine. To check go to http://RANCHER_URL/admin/ha (yes, we still use HTTP internally, it will change), and you should see this:

[<img loading="lazy" class="aligncenter size-full wp-image-736" src="https://sysadminia.files.wordpress.com/2016/05/bildschirmfoto-von-c2bb2016-05-30-16-41-23c2ab2.png" alt="Bildschirmfoto von »2016-05-30 16-41-23«" width="566" height="199" srcset="https://flypenguin.de/wp-content/uploads/2016/05/bildschirmfoto-von-c2bb2016-05-30-16-41-23c2ab2.png 566w, https://flypenguin.de/wp-content/uploads/2016/05/bildschirmfoto-von-c2bb2016-05-30-16-41-23c2ab2-300x105.png 300w" sizes="(max-width: 566px) 100vw, 566px" />][3]Nice.

#rancher

 [1]: https://github.com/sameersbn/docker-mysql
 [2]: http://docs.rancher.com/rancher/latest/en/installing-rancher/installing-server/multi-nodes/
 [3]: https://sysadminia.files.wordpress.com/2016/05/bildschirmfoto-von-c2bb2016-05-30-16-41-23c2ab2.png