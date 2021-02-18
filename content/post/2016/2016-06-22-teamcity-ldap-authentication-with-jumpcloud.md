---
title: TeamCity LDAP authentication with JumpCloud
author: penguin
type: post
date: 2016-06-22T11:53:05+00:00
url: /2016/06/22/teamcity-ldap-authentication-with-jumpcloud/
categories:
  - Infrastructure
tags:
  - jumpcloud
  - ldap
  - teamcity

---
JumpCloud looks like a great service to use LDAP without using LDAP. And I have just managed to find an error in the documentation, precisely the file "ldap-config.properties.dist".

The working configuration is:

```
# basic jumpcloud url
java.naming.provider.url=ldap://ldap.jumpcloud.com:389/

# search user for jumpcloud
java.naming.security.principal=uid=BIND_USER_NAME,ou=Users,o=ORG_ID,dc=jumpcloud,dc=com
java.naming.security.credentials=BIND_USER_PASSWORD

# unix ldap seems to use uid as username - see https://is.gd/dBPegr
teamcity.users.login.filter=(uid=$capturedLogin$)
teamcity.users.username=uid
teamcity.users.base=ou=Users,o=ORG_ID,dc=jumpcloud,dc=com
```

Seems to work nicely, now comes the finetuning.