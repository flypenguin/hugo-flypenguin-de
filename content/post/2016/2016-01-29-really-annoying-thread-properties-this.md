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
        print("I'm (%s) in directory %s"
              % (str(self), os.getcwd()))

if __name__ == "__main__":
    MyThread("/tmp").start()
    time.sleep(1)
    MyThread("/").start()
```

Result is:

```
I'm (<MyThread(Thread-1, started 140195858716416)>) in directory /
I'm (<MyThread(Thread-2, started 140195850323712)>) in directory /
```