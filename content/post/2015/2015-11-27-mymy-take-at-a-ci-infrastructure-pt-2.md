---
title: My take at a CI infrastructure, Pt.2
author: penguin
type: post
date: 2015-11-27T19:32:49+00:00
url: /2015/11/27/mymy-take-at-a-ci-infrastructure-pt-2/
categories:
  - Longer things
tags:
  - cicd
  - docker

---
For CI I want the classics - a check in (push) to the repo should be catched by TeamCity, and trigger ...

  * a build of the artifact, _once_
  * running of unit tests
  * containerizing the artifact
  * uploading it to a private Docker registry

The question was: How?

This post deals with building the code.

## Building Code

When I build code I am faced with a simple question: Which library versions do I use?

When I have multiple projects, the question becomes complex. Which version do I install on which build agent? How do I assign build tasks to agents? What if some software cannot be installed? How can I do a rollback? Or try with another lib version quickly?

The solution: Build containers. I am sure I have read about it somewhere, this is in no part an invention of my own, but I just can't find an article explaining it.

It basically goes like this. We have a docker container, which contains all necessary build libs in their development form and the build tools to build something. We pull the container, mount our checked out code dir in the container, and run the build in the controlled environment of it. We want a different set of libs? We re-build the container with them, and use the other container to build the project. Doesn't work? Go back to the previous one.

The prerequisite of this is a build process that does not change, or at least does not change for a set of projects. We use CMake, so it's the same build commands over and over: "cmake .", "make", "make test". That's it. My first working build container looks like this:

```
FROM 10.10.10.11:5000/runner/boost:9a443273-2
MAINTAINER Fly Penguin &lt;fly@flypenguin.de&gt;
RUN \
     dnf -y update && dnf -y upgrade \
  && dnf -y install cmake make gcc-c++ boost-test boost-devel \
  && dnf clean all \
  && mkdir /build
WORKDIR /build
```

Building the code now is super easy:

```
git clone ssh://... my_code
cd my_code
docker run --rm -v $(pwd):/build builder/boost:1234 cmake .
docker run --rm -v $(pwd):/build builder/boost:1234 make
docker run --rm -v $(pwd):/build builder/boost:1234 make test
```

Done.

... or? One question remains: How do I select the build container?

There are two possibilities: In the build system configuration (read: TeamCity), or in the code. I went for the code. The reason is pretty simple: I check out a specific revision of the code, I know which container it was built with. From there I can work my way up:

```
./mycode
   |--- main.c
   |--- SET_BUILD_CONTAINER
```

Guess what's in "SET\_BUILD\_CONTAINER"? Correct. Something like this:

```
my_registry_url:5000/builder/boost:abcdef98-210
```

The build configuration in TeamCity reads the file, and acts accordingly. Later I will talk more on those tags, and in the next post I talk about containerizing the binaries.