---
title: 'Django, psql & “permission denied” on migrate'
author: penguin
type: post
date: 2018-10-16T16:34:45+00:00
url: /2018/10/16/django-psql-permission-denied-on-migrate/
categories:
  - Uncategorized
tags:
  - dev
  - django
  - solution

---
I got this error:

```
psycopg2.ProgrammingError: permission denied for relation django_migrations
```

... when I wanted to do a "python manage.py migrate". [This post][1] had the solution. In short: You have to change the owner of the tables to the one specified in the Django configuration.

This is how my script looks:

```
#!/usr/bin/env bash
echo "ALTER TABLE public.django_admin_log OWNER TO <new_owner>;" | psql -U <current_owner> <database>
# ...
```



 [1]: https://stackoverflow.com/a/52060981/902327