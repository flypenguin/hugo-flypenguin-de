---
title: Syntax highlighting with wordpress
author: penguin
type: post
date: 2017-01-21T10:52:33+00:00
url: /2017/01/21/syntax-highlighting-with-wordpress/
categories:
  - Uncategorized
tags:
  - blog
  - code
  - wordpress

---
This is just a test for syntax highlighting. Which I really really really wanted to have. Even if it's WordPress and not [something cool like hugo][1].

So, let's try:

```python
import random

class RndTest:
    def __init__(self):
        print("Oy! 'this is python!")
        self.rnd_seed = random.random()

    @classmethod
    def do_nothing(cls, nothing):
        print("Doing {} now ;)".format(nothing))
```

That doesn't look so bad, right?

### How to do this

  * install the [crayon syntax highlight plugin][2]
  * when writing posts, your toolbar will have a new icon looking like this: <>
  * press it, and an "add code" dialog will open
  * do your thing
  * save
  * done

Like.

### Dislikes

  * I don't know which JS engine this thing uses (if any public one in particular)
  * I like highlight.js

(So, none really)

### Alternative plugins (untested)

To be honest, the only real contender in installation base and features seems to be [Syntax Highlighter Evolved][3]. I have not tried it, but if you don't like crayon, that looks like the one to go.

 [1]: http://gohugo.io
 [2]: https://de.wordpress.org/plugins/crayon-syntax-highlighter/
 [3]: https://de.wordpress.org/plugins/syntaxhighlighter/