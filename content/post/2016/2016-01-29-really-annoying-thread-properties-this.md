---
title: Really annoying thread properties
author: penguin
type: post
date: 2016-01-29T11:27:29+00:00
url: /2016/01/29/really-annoying-thread-properties-this/
categories:
  - Uncategorized
tags:
  - annoying
  - python
format: aside

---
This sucks monkey ass, mainly because I didn't think of that before. And that's just one example why multi-threaded (soon to be -processing, probably) applications are hard.

```
import subprocess as sp
import time
import os
from threading import Thread


class MyThread(Thread):

    def __init__(self, mydir):
        super().__init__()
        self.mydir = mydir

    def run(self):
        os.chdir(self.mydir)
        time.sleep(2)
        print(&quot;I'm (%s) in directory %s&quot;
              % (str(self), os.getcwd()))

if __name__ == &quot;__main__&quot;:
    MyThread(&quot;/tmp&quot;).start()
    time.sleep(1)
    MyThread(&quot;/&quot;).start()
```

Result is:

```
I'm (&lt;MyThread(Thread-1, started 140195858716416)&gt;) in directory /
I'm (&lt;MyThread(Thread-2, started 140195850323712)&gt;) in directory /
```