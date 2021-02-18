---
title: 'A more peaceful rant about Java & Camel'
author: penguin
type: post
date: 2020-04-16T22:23:41+00:00
url: /2020/04/16/a-more-peaceful-rant-about-java-and-camel/
categories:
  - Uncategorized
tags:
  - camel
  - java
  - rant
  - success

---
I did it. I feel like I survived something, like I am on the path on enlightenment (and it feels like raising the foot for the first step in a yourney which I know to be several AEs long). Anyway, I'm kinda happy.

What did I do? I succeeded to write this Java code using the [Apache Camel library][1] (and yes, that's it - in _total_):

```
import org.apache.camel.main.Main;

public final class CamelPusher {

    private CamelPusher() {
    }

    public static void main(String[] args) throws Exception {
        Main camelMain = new Main();
        // https://is.gd/P8SzKX
        camelMain.configure().withXmlRoutes("file:*.xml");
        camelMain.run(args);
    }

}
```

Impresive, huh?

What took me longest was to add the "file:" in the line <code class="EnlighterJSRAW" data-enlighter-language="java">camelMain.configure().withXmlRoutes("file:*.xml");</code>. Yup, really.

Code-wise, that is. It took me even longer to set this up:

```
&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;project xmlns="http://maven.apache.org/POM/4.0.0" 
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"&gt;
  &lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;
  &lt;groupId&gt;de.company.business&lt;/groupId&gt;
  &lt;artifactId&gt;camelocclient&lt;/artifactId&gt;
  &lt;version&gt;1.0.0-SNAPSHOT&lt;/version&gt;
  &lt;name&gt;CamelOCClient&lt;/name&gt;
  &lt;description&gt;The thing that transfers payment files to OC&lt;/description&gt;

  &lt;properties&gt;
    &lt;maven.compiler.source&gt;1.8&lt;/maven.compiler.source&gt;
    &lt;maven.compiler.target&gt;1.8&lt;/maven.compiler.target&gt;
  &lt;/properties&gt;

  &lt;repositories&gt;
    &lt;repository&gt;
      &lt;id&gt;spring-releases&lt;/id&gt;
      &lt;url&gt;https://repo.spring.io/libs-release&lt;/url&gt;
    &lt;/repository&gt;
  &lt;/repositories&gt;
  &lt;pluginRepositories&gt;
    &lt;pluginRepository&gt;
      &lt;id&gt;spring-releases&lt;/id&gt;
      &lt;url&gt;https://repo.spring.io/libs-release&lt;/url&gt;
    &lt;/pluginRepository&gt;
  &lt;/pluginRepositories&gt;

  &lt;dependencies&gt;
    &lt;!-- https://mvnrepository.com/artifact/org.springframework/spring-context --&gt;
    &lt;dependency&gt;
      &lt;groupId&gt;org.springframework&lt;/groupId&gt;
      &lt;artifactId&gt;spring-context&lt;/artifactId&gt;
      &lt;version&gt;5.2.5.RELEASE&lt;/version&gt;
    &lt;/dependency&gt;

    &lt;dependency&gt;
      &lt;groupId&gt;org.apache.camel&lt;/groupId&gt;
      &lt;artifactId&gt;camel-core&lt;/artifactId&gt;
      &lt;version&gt;3.2.0&lt;/version&gt;
    &lt;/dependency&gt;

    &lt;dependency&gt;
      &lt;groupId&gt;org.apache.camel&lt;/groupId&gt;
      &lt;artifactId&gt;camel-ftp&lt;/artifactId&gt;
      &lt;version&gt;3.2.0&lt;/version&gt;
    &lt;/dependency&gt;

    &lt;!-- https://mvnrepository.com/artifact/org.apache.camel/camel-spring --&gt;
    &lt;dependency&gt;
      &lt;groupId&gt;org.apache.camel&lt;/groupId&gt;
      &lt;artifactId&gt;camel-spring&lt;/artifactId&gt;
      &lt;version&gt;3.2.0&lt;/version&gt;
    &lt;/dependency&gt;

    &lt;!-- https://mvnrepository.com/artifact/org.slf4j/slf4j-simple --&gt;
    &lt;dependency&gt;
      &lt;groupId&gt;org.slf4j&lt;/groupId&gt;
      &lt;artifactId&gt;slf4j-simple&lt;/artifactId&gt;
      &lt;version&gt;1.7.30&lt;/version&gt;
    &lt;/dependency&gt;

  &lt;/dependencies&gt;

  &lt;build&gt;
    &lt;plugins&gt;
      &lt;plugin&gt;
        &lt;groupId&gt;org.codehaus.mojo&lt;/groupId&gt;
        &lt;artifactId&gt;versions-maven-plugin&lt;/artifactId&gt;
        &lt;version&gt;2.7&lt;/version&gt;
        &lt;configuration&gt;
          &lt;allowMajorUpdates&gt;false&lt;/allowMajorUpdates&gt;
          &lt;!-- https://stackoverflow.com/a/22174801/902327 --&gt;
          &lt;!-- fucking maven shit honestly those java assholes suck so badly --&gt;
          &lt;rulesUri&gt;file:///${project.basedir}/pom-versions-ruleset.xml&lt;/rulesUri&gt;
        &lt;/configuration&gt;
      &lt;/plugin&gt;
      &lt;plugin&gt;
        &lt;!-- see here: https://is.gd/HNFI0H --&gt;
        &lt;groupId&gt;org.springframework.boot&lt;/groupId&gt;
        &lt;artifactId&gt;spring-boot-maven-plugin&lt;/artifactId&gt;
        &lt;version&gt;2.1.12.RELEASE&lt;/version&gt;
        &lt;executions&gt;
          &lt;execution&gt;
            &lt;goals&gt;
              &lt;goal&gt;repackage&lt;/goal&gt;
            &lt;/goals&gt;
          &lt;/execution&gt;
        &lt;/executions&gt;
      &lt;/plugin&gt;
      &lt;plugin&gt;
        &lt;artifactId&gt;exec-maven-plugin&lt;/artifactId&gt;
        &lt;version&gt;1.6.0&lt;/version&gt;
        &lt;groupId&gt;org.codehaus.mojo&lt;/groupId&gt;
        &lt;executions&gt;
          &lt;execution&gt;            &lt;!-- Run our version calculation script --&gt;
            &lt;id&gt;git-tag&lt;/id&gt;
            &lt;goals&gt;
              &lt;goal&gt;exec&lt;/goal&gt;
            &lt;/goals&gt;
          &lt;/execution&gt;
        &lt;/executions&gt;
        &lt;configuration&gt;
          &lt;executable&gt;git&lt;/executable&gt;
          &lt;arguments&gt;
            &lt;argument&gt;tag&lt;/argument&gt;
            &lt;argument&gt;${parsedVersion.majorVersion}.${parsedVersion.minorVersion}.${parsedVersion.incrementalVersion}${parsedVersion.qualifier?}&lt;/argument&gt;
          &lt;/arguments&gt;
        &lt;/configuration&gt;
      &lt;/plugin&gt;
    &lt;/plugins&gt;
  &lt;/build&gt;

&lt;/project&gt;
&lt;!-- I FUCKING HATE MAVEN. --&gt;
```

And this, cause maybe I still don't fully get maven (and with maybe I mean definitely, and yes I know there's an error in the Makefile, I'm gonna fix it - later):

```
all: clean build prepare-test test
.PHONY: all

clean:
    rm -rf tmp
.PHONY: clean

build:
    mvn package
.PHONY: build

prepare-test: clean
    mkdir -p tmp/from_here tmp/to_here tmp/and_from_here tmp/to_here_as_well
    echo deeply &gt; tmp/from_here/i-want-to-be-moved.txt
    echo deeply &gt; tmp/and_from_here/hashtag-metoo.txt
    cp target/camelocclient-SNAPSHOT.jar tmp/
    cp test-config.xml tmp/
.PHONY: test

test:
    cd tmp; pwd; java -cp . -jar camelocclient-SNAPSHOT.jar
.PHONY: test

run: test
.PHONY: run

.PHONY: _git_dirtycheck
_git_dirtycheck:
    [[ ! -z "git status -s" ]] || (echo -e "\n\n***** working dir is dirty. *****\n\n" && false)

.PHONY: _major
_major: _git_dirtycheck
    mvn build-helper:parse-version \
        versions:set \
        -DnewVersion=\$${parsedVersion.nextMajorVersion}.0.0 \
        versions:commit

.PHONY: _minor
_minor: _git_dirtycheck
    mvn build-helper:parse-version \
        versions:set \
        -DnewVersion=\$${parsedVersion.majorVersion}.\$${parsedVersion.nextMinorVersion}.0 \
        versions:commit

.PHONY: _patch
_patch: _git_dirtycheck
    mvn build-helper:parse-version \
        versions:set \
        -DnewVersion=\$${parsedVersion.majorVersion}.\$${parsedVersion.minorVersion}.\$${parsedVersion.nextIncrementalVersion} \
        versions:commit

.PHONY: _set_version
_set_version: _git_dirtycheck
    read -p "new version: " VERSION ; \
    mvn build-helper:parse-version \
        versions:set \
        -DnewVersion=$$VERSION \
        versions:commit

.PHONY: _snap
_snap: _git_dirtycheck
    mvn build-helper:parse-version \
        versions:set \
        -DnewVersion=\$${parsedVersion.majorVersion}.\$${parsedVersion.minorVersion}.\$${parsedVersion.nextIncrementalVersion}-SNAPSHOT \
        versions:commit

.PHONY: _tag
_tag:
    git commit -am "Version bump"
    mvn build-helper:parse-version exec:exec

.PHONY: _snap_tag
_snap_tag: _snap _tag

.PHONY: _addsnap
_addsnap:
    mvn build-helper:parse-version \
        versions:set \
        -DnewVersion=\$${parsedVersion.majorVersion}.\$${parsedVersion.minorVersion}.\$${parsedVersion.incrementalVersion}-SNAPSHOT \
        versions:commit

.PHONY: _release
_release: _git_dirtycheck
    mvn build-helper:parse-version \
        versions:set \
        -DnewVersion=\$${parsedVersion.majorVersion}.\$${parsedVersion.minorVersion}.\$${parsedVersion.incrementalVersion} \
        versions:commit

.PHONY: major
major: _major _tag build _snap_tag

.PHONY: minor
minor: _minor _tag build _snap_tag

.PHONY: patch
patch: _patch _tag build _snap_tag

.PHONY: addsnap
addsnap: _addsnap

.PHONY: rel
rel: _release _tag build _snap_tag

.PHONY: release
release: rel

.PHONY: set-version
set-version: _set_version _tag build _snap_tag

.PHONY: setver
setver: set-version
```

So this is it. That abomination took me days to build.

So now I can do "<code class="EnlighterJSRAW" data-enlighter-language="generic">make release</code>", get a .jar file, and place something like this next to it:

```
&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;!--
  DOKUMENTATION:
  https://camel.apache.org/manual/latest/spring.html
--&gt;

&lt;routes xmlns="http://camel.apache.org/schema/spring"&gt;
    &lt;route&gt;
        &lt;from uri="file:from_here"/&gt;
        &lt;to uri="file:to_here"/&gt;
    &lt;/route&gt;
    &lt;route&gt;
        &lt;from uri="file:and_from_here"/&gt;
        &lt;to uri="file:to_here_as_well"/&gt;
    &lt;/route&gt;
&lt;/routes&gt;
```

... having a freely configurable, all-purpose, no-nonsense Camel engine to my disposal, which I can then deploy to any host of my liking to do things which are useful. In that case - it's supposed to transfer files (the "left" side of the system, a couple of deployments) to S3, and then pull them out (the "right" side of it, one deployment) into a folder on a target machine.

This is it, this is all, this is basically the most trivial use case ever invented, and it's supposed to be replaced with something "real". Let's see how long it takes to get there.

Now, am I happy? Yes. Is this ... "good"? No. Does it work? Probably (it does with a file server / sftp transfer system).

But the best part is: I kinda, sorta understand a little bit of what I did. And I am starting to think that Camel might actually be awesome, and maven actually useful - once you start understanding the concepts and ecosystem. But that's just a vague possibility, given that I still don't 😉 .

I [published the files here][2] as well.

 [1]: https://camel.apache.org/
 [2]: https://gist.github.com/flypenguin/8862f70057ec6e26dffae364bd2f564e