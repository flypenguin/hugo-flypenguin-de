---
title: My take at a CI infrastructure, Pt.3
author: penguin
type: post
date: 2015-11-27T19:55:38+00:00
url: /2015/11/27/mymy-take-at-a-ci-infrastructure-pt-3/
categories:
  - Longer things
tags:
  - cicd
  - docker

---
All right, back again. Much text here. Let's talk about ...

## Containerizing The Binaries

We are [done with the build][1], now we have a binary. I went for something simple: Who knows best how to put this into a container? The dev guy. Cause he knows what he needs, where he needs it, and where it can be found after the build.

But containerizing it should be not hard, given a moderately complex software with a couple of well thought-of build scripts. So I went for this:

```
/my_code/
   |--- docker/               # !!!
   |      |--- prepare.sh     # !!! optional
   |      |--- Dockerfile     # !!! required ;)
   |--- main.c
   |--- SELECT_BUILD_CONTAINER
   |--- build/                # !!! created by the build
          |--- ...            # !!!
```

Now it get's straightforward: The build scripts in TeamCity ...

  * look for the docker directory, change into it,
  * execute the "prepare.sh" script if found,
  * build a container from the Dockerfile,
  * tag the container and
  * push it into the registry (which is configured centrally in TeamCity)

## Tagging the containers

A docker cotainer is referenced like this:

```
some/thing:and_a_tag
```

How do we choose how to name the container we just built? Two versions.

For projects which contain _nothing but a Dockerfile_ (which we have, cause our build containers are also versioned, of course), I enforce this combination:

```
Repository name: docker-one-two
... will yield:  one/two:1234abc9-321 (as container repo/name:tag)
```

The build script enforces the scheme "docker-one-two", and takes "one" and "two" automatically as names for the container. Then "1234abc9" is the git commit id (short), and "321" is the build number.

Why not only the git commit ID? Because between builds, the same result is not guaranteed if executing the build again. If you build a container, and the execution contains an "apt-get update", two builds a week apart will not result in the same contents.

For "simple" or "pure" code builds I use the following scheme:

```
Repository name: some-thing
... will yield:  some/thing:1234abc9
```

Same reasoning.

In both cases a container "some/thing:latest" is also tagged and pushed.

Now when we run a software container, we can see

  * with which container it was built (by looking at "SET\_BUILD\_CONTAINER"),
  * which base container was used to build the software container (by looking at "docker/Dockerfile")
  * and we can do this cause we know the git commit ID.

For each base container (or "pure" Dockerfile projects), we extend this with a build number.

## Done.

So this is my state so far. If anyone reads this, I would be interested in comments or feedback.



 [1]: https://sysadminia.wordpress.com/2015/11/27/mymy-take-at-a-ci-infrastructure-pt-2/