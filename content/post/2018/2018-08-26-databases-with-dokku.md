---
title: Databases with dokku
author: penguin
type: post
date: 2018-08-26T12:18:47+00:00
url: /2018/08/26/databases-with-dokku/
categories:
  - CICD
  - Development
  - Docker
  - Infrastructure
  - Linux
tags:
  - cicd
  - docker
  - dokku
  - hosting
  - postgres

---
This is part 2 of a couple of blog posts about dokku, a amazing little Heroku clone.

In the [previous post][1] I showed how to set up Dokku on a DigitalOcean droplet, and deployed a little hello-world container with a single git push. The reason why I wanted dokku thoug was the need of a database. As said - hosting comes cheap, databases usually come either expensive or with limited flexibility, or just too annoying configuration effort.

Dokku is the perferct middle ground. Let's see why.

For me it was the existing [postgres plugin][2] which you can simply install and use. The whole process is incredibly easy, takes wbout two commands, and looks like this (let's assume our "hello world" container uses a database):

```
$ sudo dokku plugin:install https://github.com/dokku/dokku-postgres.git postgres
```

That's it, again.

```
$ dokku postgres:create hello-world

dokku postgres:create hello-world
Waiting for container to be ready
Creating container database Securing connection to database
=====> Postgres container created: hello-world
=====> Container Information
       Config dir: /var/lib/dokku/services/postgres/hello-world/config
       Data dir: /var/lib/dokku/services/postgres/hello-world/data
       Dsn: postgres://postgres:bd6b0725d710bb5a662bb628eee787b1@dokku-postgres-hello-world:5432/hello_world
       Exposed ports: -
       Id: 785ef252c748ed85739d1d6ad375a1e1bd66e925ac79358e9ffaa30ab852d6c0
       Internal ip: 172.17.0.9
       Links: -
       Service root: /var/lib/dokku/services/postgres/hello-world
       Status: running
       Version: postgres:10.2

$ docker ps

CONTAINER ID   IMAGE                      COMMAND                  CREATED         STATUS         PORTS      NAMES
cc99cccacf2c   dokku/hello-world:latest   "/bin/sh -c 'php-fpm…"   2 minutes ago   Up 2 minutes   80/tcp     hello-world.web.1
785ef252c748   postgres:10.2              "docker-entrypoint.s…"   5 minutes ago   Up 5 minutes   5432/tcp   dokku.postgres.hello-world
[...]
```

This creates a database container with postgres 10.2, as you can see. You can influence a lot of behavior by using environment variables, see the GitHub page for more info.

Then you link the container to the running app:

```
$ dokku postgres:link hello-world hello-world
-----> Setting config vars
       DATABASE_URL: postgres://postgres:bd6b0725d710bb5a662bb628eee787b1@dokku-postgres-hello-world:5432/hello_world
-----> Restarting app hello-world
-----> Releasing hello-world (dokku/hello-world:latest)...
-----> Deploying hello-world (dokku/hello-world:latest)...
-----> Attempting to run scripts.dokku.predeploy from app.json (if defined)
-----> No Procfile found in app image
-----> DOKKU_SCALE file found (/home/dokku/hello-world/DOKKU_SCALE)
=====> web=1
-----> Attempting pre-flight checks
       For more efficient zero downtime deployments, create a file CHECKS.
       See http://dokku.viewdocs.io/dokku/deployment/zero-downtime-deploys/ for examples
       CHECKS file not found in container: Running simple container check...
-----> Waiting for 10 seconds ...
-----> Default container check successful!
-----> Running post-deploy
-----> Configuring hello-world.my-paas.for-myself.com...(using built-in template)
-----> Creating http nginx.conf
-----> Running nginx-pre-reload Reloading nginx
-----> Setting config vars DOKKU_APP_RESTORE: 1
-----> Found previous container(s) (14c349cb496d) named hello-world.web.1
=====> Renaming container (14c349cb496d) hello-world.web.1 to hello-world.web.1.1535285386
=====> Renaming container (cc99cccacf2c) serene_bassi to hello-world.web.1
-----> Attempting to run scripts.dokku.postdeploy from app.json (if defined)
-----> Shutting down old containers in 60 seconds
=====> 14c349cb496d95cc4be1833f2e7f6ef2bef099a37c2a22cd4dcdb542f09bea0f
=====> Application deployed:
       http://hello-world.my-paas.for-myself.com
```

And done.

What happened? You have now the environment variable `$DATABASE_URL` set in the hello-world app, that's why the restart was necessary (which you can postpone, if you want, but you probably need it now, right?).

Let's check:

```
$ docker exec -ti hello-world.web.1 /bin/sh

[now in the container]

# env | grep DATABASE
DATABASE_URL=postgres://postgres:bd6b0725d710bb5a662bb628eee787b1@dokku-postgres-hello-world:5432/hello_world
```

That's it. Super easy. Now if you're using Django, you could use [kennethreitz/dj-database-url][3] to automatically parse and use it, and you're done. (Probably every framework has something similar, so just have a look).

 [1]: https://flypenguin.de/2018/08/25/build-your-own-paas-with-dokku/
 [2]: https://github.com/dokku/dokku-postgres
 [3]: https://github.com/kennethreitz/dj-database-url