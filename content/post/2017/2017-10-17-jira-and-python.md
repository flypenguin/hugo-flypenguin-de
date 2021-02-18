---
title: JIRA and Python
author: penguin
type: post
date: 2017-10-17T18:13:08+00:00
url: /2017/10/17/jira-and-python/
categories:
  - Snippets
tags:
  - jira
  - python

---
I really came to hating JIRA with a passion. And now I have to create about 350 tickets.

Naturally I don't do this by hand. But using the JIRA API is kind of ... hard, there is [a Python library][1], but usage is rather sparsely documented, and this whole thing is just annoying as hell.

Although when you did it, it's quite simple. Here's an example of how to create a ticket with due date and estimate set from a CSV file:

```python
from pprint import pprint, pformat
from getpass import getpass

import jira
 
 
def do_shit():
    url = read("JIRA url:")
    username = read("JIRA username:")
    password = getpass("JIRA password:")
    jira_inst = jira.JIRA(url, basic_auth=(username, password))
 
    test = {
        "project":          {"key": "TEST"},
        "issuetype":        {"name": "Task"},
 
        "labels":           ["deleteme", "whatisthis"],
 
        "summary":          "woho",
        "description":      "wohooooo",
        "duedate":          "2017-11-11",
 
        "timetracking":    {"originalEstimate": "4d"},
    }
 
    created = jira_inst.create_issues([test])
    pprint(created)
 
 
if __name__ == "__main__":
    do_shit()
```

So simple.

 [1]: https://jira.readthedocs.io/en/master/