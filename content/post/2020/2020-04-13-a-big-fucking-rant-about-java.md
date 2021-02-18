---
title: A big fucking rant about Java
author: penguin
type: post
date: 2020-04-13T17:47:17+00:00
url: /2020/04/13/a-big-fucking-rant-about-java/
categories:
  - Development
tags:
  - java
  - rant

---
Well, I'm back to writing Java. It was supposed to be simple - an Apache Camel .jar file which just reads the route definitions from a nearby *.XML file in the same directory. So it can be re-used, because we have a couple of dead simple use cases which _just_ require a Camel runtime and no special Java Beans.

Simple, right.

I'm playing Doom Eternal for fun at the moment. And just thinking of this abomination called "maven", which Java uses to build shit makes me wish for all maven devs to rot in hell eternally. This piece of junk is basically undocumented. Don't get me wrong, theres _tons_ of written stuff, intros, tutorials, etc. about maven. It's just after reading all of them you're still the same code-copy-pasting monkey you were before. Once you hunted down the snippet on Stackoverflow you go on to the next blackbox.

Examples? Sure.

  * Versions in build plugins. Maven complains, but countless examples I found don't use any version. (A Java illness, it seems all examples _have_ to be incomplete, otherwise it's no fun for the developer, right?).
  * Complexity. You can configure the versions-maven-plugin to update the version numbers. To ignore alpha / beta / whatever versions you have to write a regex expression in a _separate file_ and import that to the pom.xml. Of course you write the import statement in the _configuration section_ of the plugin. Writing the rules in there? Naaaaah, too easy my friend. Extra file please.
  * Commands. There's no help. There's only "plugins". No starting point, no idea what's "default" and what the 1, 2, 3 steps are to get your app built. Just a bunch of horseshit you try out until your app is built. Or you think it is. (Or back to Stackoverflow, take the first example that works and go on to the next blackbox ...)
  * Reposities. It seems I built my app several times correctly, then I configured some repositories, and suddenly it was built ... more correctly. Don't ask me why.

Let's continue with Spring. They once were (and probably still are) on the forefront of "modern", modularized, reusable, enterprise-pattern-enabled, lightweight Java. Problem is, that's _still_ way too complex for any sane human being.

Examples? Sure.

  * Configuration. I try to use Apache Camel. Seems to be a great product, all is configurable using either Spring DSL or code. Now if you want to do it with Spring, until today I invested _days_ to find out how to read the fucking configuration from that god-damned configuration file. It's _just. Not. Possible._ Sure, countless examples on the net, but that leads me to ...
  * the documentation. Just some random notes:
      * The examples there are just don't cut it and come basically without any documentation.
      * All links which sound like they could help lead to a 404 page.
      * "if you want to do X you can 'easily' do this using Spring XML" is a sentence I want to burn now, because ... well, there's no _example_, usually. You're just being redirected to some Spring page which explains the Spring concepts in epic detail, with no connection whatsoever to the actual problem with the actual fucking library.
      * _If_ they include a code snippet it's _always_ without any context. So you have the critical 3 lines, but if you want to include them into an actual application, you wonder about imports, namespaces, setup boilerplates, and countless other things _every. Single. Time_.
      * In summary it seems the docs are there for the pros who want to just quickly look something up they inconveniently forgot cause _they already know a shit ton of shit_. If you're new to this? Yeah, go ahead, it's easy, here's a link to the Spring concepts.
      * Oh yeah, and it might be it's just plainly, utterly, horribly _wrong_. But Java being _nothing_ but enterprise, it's at lease _consistently_ wrong all the time.

I think I managed to solve the problem just now. Unfortunately this cost me _days_, and only because of the most brainwashed, brainfucked eco-system I can imagine.

(Disclaimer: I think Java has some really cool parts as well, just _right now_ - I honestly don't care. At all.)