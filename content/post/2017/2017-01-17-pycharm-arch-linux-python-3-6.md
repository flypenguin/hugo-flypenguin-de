---
title: 'PyCharm, Arch linux & Python 3.6'
author: penguin
type: post
date: 2017-01-17T14:45:29+00:00
url: /2017/01/17/pycharm-arch-linux-python-3-6/
categories:
  - Snippets
tags:
  - arch
  - fix
  - pycharm
  - python

---
Love Python. Love PyCharm. Love Arch Linux.

Unfortunately Arch sneakily updated Python to 3.6. Cool, new version ... but hey, why don't my debug runs in PyCharm work any more??

```default
ImportError: libpython3.5m.so.1.0: cannot open shared object file: No such file or directory
```

Yup, pretty confusing. It seems unable to find shared python 3.5 library. Well. After some cursing, turns out the solution is pretty simple (if you know what to do):

  * get [pyenv][1]
  * use pyenv to install Python 3.5.2, but **with -enable-shared option set**
  * use this python version for PyCharm projects (it does not matter if it's in a virtualenv or not)

Like this:

```default
$ PYTHON_CONFIGURE_OPTS="--enable-shared" pyenv install 3.5.2
[...]
$ sudo $HOME/.pyenv/versions/3.5.2/bin/python "/opt/pycharm-professional/helpers/pydev/setup_cython.py" build_ext --inplace
[...]
$ _
```

That solved it for me 🙂

###### References:

  * <https://github.com/yyuu/pyenv/issues/65>

 [1]: https://github.com/yyuu/pyenv