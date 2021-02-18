---
title: 'Puppet Stages & Notify'
author: penguin
type: post
date: 2013-07-04T10:10:39+00:00
url: /2013/07/04/puppet-stages-notify/
categories:
  - Uncategorized
tags:
  - hmm
  - puppet
  - sysadmin

---
Die notify Funktion von Puppet hat eine seltsame Eigenschaft, die ich persönlich wenig nachvollziehbar finde. Sie impliziert einen "before"-Zusammenhang zwischen der Resource, die benachrichtigt, und der benachrichtigten. Das führt zu unpraktischen Komplikationen. Der Versuch einer Herleitung:

> <p style="text-align:left;">
>   service { "tomcat" : ensure => running }
> </p>
> 
> <p style="text-align:left;">
>   file { "/etc/tomcat.conf" : notify => Service["tomcat"] }
> </p>

<p style="text-align:left;">
  Das funktioniert bestens, und bedeutet, dass zuerst die file-Direktive abgearbeitet wird, und _dann_ der Service. Darauf aufbauend kann ich mir sehr gut folgende Erweiterung vorstellen:
</p>

> <p style="text-align:left;">
>   class { "deployment::basic_app_server" : }
> </p>
> 
> <p style="text-align:left;">
>   file { "/etc/tomcat.conf" : notify => Service["tomcat"] }
> </p>

<p style="text-align:left;">
  Nehmen wir an in "deployment::basic_app_server" werden einige zentrale Dinge geregelt, darunter auch der tomcat-Service. Die Absicht: Ich möchte mich darauf verlassen können, dass spezifische Module auf einem bestimmten Konfigurationsstand aufsetzen. Dafür wurden stages eigentlich erfunden (meines Erachtens nach), also erweitern wir zu:
</p>

> stage { "preparation" : before => Stage["main"] }
> 
> class { "deployment::basic\_app\_server" : stage => "preparation" }
> 
> file { "/etc/tomcat.conf" : notify => Service["tomcat"] }

Und ... bumm. Da notify eine "file-before-service" (da Service wie angenommen in deployment::basic\_app\_server konfiguriert wird) impliziert, aber die Stages eine "service-before-file"-Beziehung verlangen, geht das nicht. Schön ist das nicht, finde ich.