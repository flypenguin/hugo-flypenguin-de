---
title: Build your own PaaS with Dokku
author: penguin
type: post
date: 2018-08-25T18:10:47+00:00
url: /2018/08/25/build-your-own-paas-with-dokku/
categories:
  - CICD
  - Development
  - Docker
  - Infrastructure
tags:
  - cicd
  - digitalocean
  - docker
  - dokku
  - heroku
  - hosting
  - howto

---
I was looking for some "play" deployment method for a couple of things I want to try out. Most of them require a database. And it should be cheap, cause I don't have any load on them and don't earn any money, so I don't want to spend basically no money if possible. The usual suspects are too expensive - AWS, Heroku, etc.

So I looked around and found [Dokku][1].

Dokku is a set of - hang on - shell scripts - which basically emulate Heroku on a machine of your own. It's integrated with Digital Ocean droplets out of the box, if you want it. And the whole thing is 5 € / month, which is perfect. It also integrates with a Dockerfile based deployment, so you do git push and everything just works.

It's amazing.

This is how you get started. But before you can get started, **you need a domain** you control, either on AWS or any other hoster. This is for routing traffic to your deployments later. You also **need a public SSH key**, or better a public / private key pair. Once you have both you can ...

  1. create a Digital Ocean account, and ...
  2. add your SSH public key to your account, and ...
  3. in that account, create a new droplet with a "Dokku" image preinstalled.
  4. Wait until the droplet finished provisioning.

<div id='gallery-1' class='gallery galleryid-1096 gallery-columns-3 gallery-size-thumbnail'>
  <figure class='gallery-item'>

  <div class='gallery-icon portrait'>
    <a href='https://flypenguin.de/2018/08/25/build-your-own-paas-with-dokku/screenshot-6/'><img width="150" height="150" src="https://flypenguin.de/wp-content/uploads/2018/08/screenshot-6-150x150.png" class="attachment-thumbnail size-thumbnail" alt="" loading="lazy" /></a>
  </div></figure><figure class='gallery-item'>

  <div class='gallery-icon portrait'>
    <a href='https://flypenguin.de/2018/08/25/build-your-own-paas-with-dokku/screenshot-7/'><img width="150" height="150" src="https://flypenguin.de/wp-content/uploads/2018/08/screenshot-7-150x150.png" class="attachment-thumbnail size-thumbnail" alt="" loading="lazy" /></a>
  </div></figure><figure class='gallery-item'>

  <div class='gallery-icon landscape'>
    <a href='https://flypenguin.de/2018/08/25/build-your-own-paas-with-dokku/screenshot-5/'><img width="150" height="150" src="https://flypenguin.de/wp-content/uploads/2018/08/screenshot-5-150x150.png" class="attachment-thumbnail size-thumbnail" alt="" loading="lazy" /></a>
  </div></figure>
</div>

While the droplet is being created, you can also create a project locally to test it later:

```sh
$ mkdir dokku-test
$ cd dokku-test
$ git init
$ echo "FROM tutum/hello-world" &gt; Dockerfile
$ git add Dockerfile
$ git commit -m "Initial commit"
```

In this little test project we only create a Dockerfile from an hello-world image which displays "Hello world" in a browser so we can verify it worked.

Once the droplet is done, you can start setting up your personal little PaaS. First, you have to **configure your DNS**. We will set up a wildcard entry for our deployments, and a non-wildcard entry for git. Let's assume your domain is `for-myself.com`, then you would add ...

  * `my-paas.for-myself.com` , type "A" (or "AAAA" if you are IPv6) to your droplet IP
  * `*.my-paas.for-myself.com` just the same

Then you **SSH into your droplet, and create your dokku project**. (This is something you have to do for every project). All you have to do for this is:

```default
$ ssh root@DROPLET_IP
~# dokku apps:create hello-world
-----&gt; Creating hello-world... done
~# _
```

Done.

Now you configure a git remote URL for your project, and push it:

```default
$ git remote add dokku dokku@my-paas.for-myself.com:hello-world
```

Again - done. If you push your project now (assuming DNS is already set), everything should happen automagically:

```default
$ git push --set-upstream dokku master
X11 forwarding request failed
Enumerating objects: 3, done.
Counting objects: 100% (3/3), done.
Writing objects: 100% (3/3), 241 bytes | 241.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0)
-----&gt; Cleaning up...
-----&gt; Building hello-world from dockerfile...
remote: build context to Docker daemon  2.048kB
Step 1/1 : FROM tutum/hello-world
latest: Pulling from tutum/hello-world
658bc4dc7069: Pulling fs layer
[... TRUNCATED ...]
983d35417974: Pull complete
Digest: sha256:0d57def8055178aafb4c7669cbc25ec17f0acdab97cc587f30150802da8f8d85
Status: Downloaded newer image for tutum/hello-world:latest
 ---&gt; 31e17b0746e4
Successfully built 31e17b0746e4
Successfully tagged dokku/hello-world:latest
-----&gt; Setting config vars
       DOKKU_DOCKERFILE_PORTS:  80/tcp
-----&gt; Releasing hello-world (dokku/hello-world:latest)...
-----&gt; Deploying hello-world (dokku/hello-world:latest)...
-----&gt; Attempting to run scripts.dokku.predeploy from app.json (if defined)
-----&gt; No Procfile found in app image
-----&gt; DOKKU_SCALE file not found in app image. Generating one based on Procfile...
-----&gt; New DOKKU_SCALE file generated
=====&gt; web=1
-----&gt; Attempting pre-flight checks
       For more efficient zero downtime deployments, create a file CHECKS.
       See http://dokku.viewdocs.io/dokku/deployment/zero-downtime-deploys/ for examples
       CHECKS file not found in container: Running simple container check...
-----&gt; Waiting for 10 seconds ...
-----&gt; Default container check successful!
-----&gt; Running post-deploy
-----&gt; Creating new /home/dokku/hello-world/VHOST...
-----&gt; Setting config vars
       DOKKU_PROXY_PORT_MAP:  http:80:80
-----&gt; Configuring hello-world.my-paas.for-myself.com...(using built-in template)
-----&gt; Creating http nginx.conf
-----&gt; Running nginx-pre-reload
       Reloading nginx
-----&gt; Setting config vars
       DOKKU_APP_RESTORE:  1
=====&gt; Renaming container (14c349cb496d) amazing_snyder to hello-world.web.1
-----&gt; Attempting to run scripts.dokku.postdeploy from app.json (if defined)
=====&gt; Application deployed:
       http://hello-world.my-paas.for-myself.com

To my-paas.for-myself.com:hello-world
 * [new branch]      master -&gt; master
Branch 'master' set up to track remote branch 'master' from 'dokku'.
```

And if you open your URL now (which is `hello-world.my-paas.for-myself.com`) you should see this image:

<img loading="lazy" class="size-medium wp-image-1100 aligncenter" src="https://flypenguin.de/wp-content/uploads/2018/08/screenshot-8-266x300.png" alt="" width="266" height="300" srcset="https://flypenguin.de/wp-content/uploads/2018/08/screenshot-8-266x300.png 266w, https://flypenguin.de/wp-content/uploads/2018/08/screenshot-8-768x868.png 768w, https://flypenguin.de/wp-content/uploads/2018/08/screenshot-8-906x1024.png 906w, https://flypenguin.de/wp-content/uploads/2018/08/screenshot-8.png 970w" sizes="(max-width: 266px) 100vw, 266px" />

Now, for 5 € / month you get:

  * A heroku-like, no-nonsense, fully automated, git-based deployment platform
  * A server which you control (and have to maintain, okay, but on which you can deploy ...)
  * A database (or many of them - dokku provides great integration for databases btw; more on that in another post)
  * Publicly reachable deployments (for customers, testing, whatever)
  * Let's Encrypt certificates (dokku provides support for these as well, again more in a later post)
  * And for 1 € more (it's always 20% of the base price) you get backups of your system)

That's absolutely incredible. Oh, and did I mention that the maintainers are not only friendly, but also super responsive and incredibly helpful on [Slack][2]?

 [1]: http://dokku.viewdocs.io/dokku/
 [2]: https://glider-slackin.herokuapp.com/