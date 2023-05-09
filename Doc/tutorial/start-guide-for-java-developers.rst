.. _tut-for-java-developers:

****************
Python Guide for Java Developers
****************

.. topic:: Abstract

    The purpose of this guide is to help any *experienced Java developers* with situations like this: we are writing
    a Python project (We love Java, but for some reason we end up being in a situation where you have to put it in
    Python) This guide comes with a comparative strategy: teach Python by linking Java with Python's equivalent. We
    know Maven and we will learn what the "Maven" equivalent in Python is; We know Python doesn't play with `Class` in
    the same way how Java does and this guide teaches us about that.

    This guide assumes reader know all the basics of Python; we know what a Python `dict` is and we know there is
    something called Python shell that executes Python script in your terminal, etc.


Useful Books & Links
=====

- [Python for the Busy Java Developer](https://trello.com/c/Sm7reFB9)
- https://www.askpython.com/


Installing Python 3 on Mac OS X
===============================

The version shipped with OS X may be out of date from the
`official current Python release <https://www.python.org/downloads/mac-osx>`_, which is considered the stable production
version.

Before installing Python, we'll need to install GCC. GCC can be obtained by downloading
`Xcode <https://developer.apple.com/xcode>`_

.. note::

  If we perform a fresh install of Xcode, we will also need to add the commandline tools by running
  ``xcode-select --install`` in terminal.

While OS X comes with a large number of Unix utilities, those familiar with Linux systems will notice one key component
missing: a package manager. `Homebrew <https://brew.sh/>`_ fills this void.

To `install Homebrew <https://brew.sh/#install>`_, open Terminal or one's favorite OS X terminal emulator and run::

   $ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"

Now, we can install Python 3::

   $ brew install python

Python Standards
================

If you know JVM Specificaiton or Java Language Specification, you might want to know the "equivalents" of them in
Python, this is the place you go - `PEP's <https://www.python.org/dev/peps/>`_

Must-read PEPs:

- `how to document Python code <https://www.python.org/dev/peps/pep-0257/>`_
- `code style <https://www.python.org/dev/peps/pep-0008/#constants>`_
- `variable annotation <https://www.python.org/dev/peps/pep-0526/>`_
- `Type Hints <https://www.python.org/dev/peps/pep-0484/>`_

How to create a Python "maven" package
======================================

You know all the details about maven. Now you wonder what if I would like to make a Python package? This is where you
start - https://python-packaging.readthedocs.io/en/latest/index.html (If the link is not working, checkout the doc
source code at https://github.com/QubitPi/python-packaging)

setuptools
----------

- *access resource files* (remember the Maven `resource` directory?):
  https://setuptools.readthedocs.io/en/latest/pkg_resources.html

package and class
-----------------

Python treats `Class` differently. They call it `module` and a `module` could contain multiple `classes`. Collect some
related class to one module(source file), and collect some related module to one package

Checkstyle
----------

`Pylint <https://www.pylint.org/>`_ is the Python's equivalent of
`checkstyle <https://checkstyle.sourceforge.io/index.html>`_. People know that to make checkstyle useful in Maven, they
use `maven-checkstyle-plugin <https://maven.apache.org/plugins/maven-checkstyle-plugin/index.html>`_. For setuptools in
Python, it is `setuptools-lint <https://pypi.org/project/setuptools-lint/>`_ and
`black <https://pypi.org/project/black/>`_

PyLint FAQ
^^^^^^^^^^

Overriding max-line-length in Individual File
"""""""""""""""""""""""""""""""""""""""""""""

   pylint --max-line-length=240

How to Fix "c0209: formatting a regular string which could be a f-string (consider-using-f-string)"
"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

Let's consider this small script::

   name = 'world'

   a = 'my hello %s' % name

   print(a)

   b = 'again this name is {}'.format(name)

   print(b)

If we run Pylint 2.11.0+ on it, we get a few errors:

.. figure:: pylint-f-string-check.png

If it's OK for you to update to f-string, then that's the recommended way. How you do that depends on how you're
formatting your strings but in doubt you can
`check this article <https://miguendes.me/73-examples-to-help-you-master-pythons-f-strings>`_ to learn the myriad ways
you can use a f-string.

In my case, replacing `%` and `str.format` becomes::

   name = 'world'

   a = f'my hello {name}'

   print(a)

   b = f'again this name is {name}'

   print(b)

If we re-run Pylint, we get:

.. figure:: pylint-f-string-check-passed.png

Documentation
-------------

Language
^^^^^^^^

You will find multiples for writing Python docs, I recommend
`reStructuredText <https://docutils.sourceforge.io/rst.html>`_ which is the supported language by the popular
Python documentation generator `Sphinx <https://www.sphinx-doc.org/en/master/>`_. Note that the
`official Python documentation <https://docs.python.org/3/>`_ is generated by Sphinx.

Documentation Generator
^^^^^^^^^^^^^^^^^^^^^^^

Documentation is generated using `maven site <https://maven.apache.org/plugins/maven-site-plugin/>`_.

Unlike maven which makes it hard to change the style/look of the generated site, you get little bit of freedom on the
style of the site for Python using `Sphinx Theming <https://www.sphinx-doc.org/en/master/usage/theming.html>`_

Example
"""""""

Suppose we have a python progject with the following directory structure::

   project-foo
     └── module-x

where module-x bundles several `.py` source files.

To generate doc for module-x module, switch to the project-foo directory and run::

   sphinx-quickstart docs

This will generate `docs` directory under the project-foo directory, which makes it look like the following::

   project-foo
     └── module-x
     └── docs

Now modify the `docs/source/conf.py`::

   import os
   import sys
   sys.path.insert(0, os.path.abspath('../../module-x/'))

   extensions = [
       'sphinx.ext.autodoc'
   ]

   html_theme = 'classic'

Next, generate the package .rst file for module-x::

   sphinx-apidoc -o docs/source module-x/

Lastly, generte the HTML, which will be put into the "docs/build/html" directory as specified in the following command::

   sphinx-build -b html docs/source/ docs/build/html

Test
----

- `doctest <https://docs.python.org/3/library/doctest.html#module-doctest>`_: commenting and testing at the same time
  (unit test)
- `mock <https://pypi.org/project/mock/>`_: Python's mocking framework
- `tox <https://tox.readthedocs.io/en/latest/>`_: test package under various python version environment
- `requests-mock <https://requests-mock.readthedocs.io/en/latest/>`_: Mock HTTP
- `FreezeGun <https://github.com/spulec/freezegun>`_: mock datetime module

Code Coverage
^^^^^^^^^^^^^

https://coverage.readthedocs.io/en/coverage-5.1/

Test Report
^^^^^^^^^^^

- `pytest-html <https://pypi.org/project/pytest-html/>`_

Commonly Used Libraries
=======================

You know the best industry Java JSON library is Jackson so you always use it. You might wonder that are those popular
equivalents in Python. Here they are

HTTP
----

https://requests.readthedocs.io/en/master/ .

URL-related
^^^^^^^^^^^

`urllib.parse <https://docs.python.org/3/library/urllib.parse.html>`_

Hashing
-------

- `hashlib <https://docs.python.org/3/library/hashlib.html>`_
- `hmac <https://docs.python.org/3/library/hmac.html#module-hmac>`_

Syntax for Java Developers
==========================

Global Variables
----------------

To change the value of a global variable inside a function, refer to the variable by using the `global` keyword::

   x = "awesome"

   def myfunc():
     global x
     x = "fantastic"

   myfunc()

   print("Python is " + x)

How do I Get Time of a Python Script's Execution?
-------------------------------------------------

The simplest way in Python::

   import time
   start_time = time.time()
   main()
   print("--- %s seconds ---" % (time.time() - start_time))

This assumes that your program takes at least a tenth of second to run. Prints::

   --- 0.764891862869 seconds ---

Python Logging
--------------

https://docs.python.org/3.1/library/logging.html

`*args` and `*kwargs` in Python
-------------------------------

`*args`
^^^^^^^

The special syntax `*args` in function definitions in python is used to pass a variable number of arguments to a
function. It is used to pass a non-keyworded, variable-length argument list.

- The syntax is to use the symbol `*` to take in a variable number of arguments; by convention, it is often used with
  the word args.
- What `*args` allows you to do is take in more arguments than the number of formal arguments that you previously
  defined. With `*args`, any number of extra arguments can be used on to your current formal parameters (including
  zero extra arguments).
- Using the `*`, the variable that we associate with the `*` becomes an iterable meaning you can do things like
  iterate over it, run some higher order functions such as map and filter, etc.

   def myFun(arg1, *argv):
       print ("First argument :", arg1)
       for arg in argv:
           print("Next argument through *argv :", arg)

   myFun("A", "B", "C", "D")

Output::

   First argument : A
   Next argument through *argv : B
   Next argument through *argv : C
   Next argument through *argv : D

`*kwargs`
^^^^^^^^^

The special syntax `*kwargs` in function definitions in python is used to pass a keyworded, variable-length argument
list. We use the name `kwargs` with the double star. The reason is because the double star allows us to pass through
keyword arguments (and any number of them).

- A keyword argument is where you provide a name to the variable as you pass it into the function.
- One can think of the `kwargs` as being a dictionary that maps each keyword to the value that we pass alongside it.
That is why when we iterate over the kwargs there doesn't seem to be any order in which they were printed out::

   def myFun(arg1, *kwargs):
       for key, value in kwargs.items():
           print ("%s == %s" %(key, value))

   myFun("Foo", first ='Bar', mid ='Bat', last='Baz')

Output::

   last == Bar
   mid == Bat
   first == Baz

Make function return multiple return values
-------------------------------------------

You can use a `typing.Tuple` type hint (to specify the type of the content of the tuple, if it is not necessary, the
built-in class `tuple` can be used instead)::

   from typing import Tuple

   def greeting(name: str) -> Tuple[str, List[float], int]:
       # do something
       return a, b, c

Debug Python
============

UnicodeEncodeError: 'ascii' codec can't encode character u'\xa0' in position 20: ordinal not in range(128)
----------------------------------------------------------------------------------------------------------

You need to read the Python [Unicode HOWTO](https://docs.python.org/2.7/howto/unicode.html). This error is the very
first example.  Basically, stop using str to convert from unicode to encoded text / bytes.

Instead, properly use `.encode()` to encode the string::

   str(foo).encode('utf-8')

SyntaxError- EOL while scanning string literal
----------------------------------------------

An EOL ( End of Line ) error indicates that the Python interpreter expected a particular character or set of characters
to have occurred in a specific line of code, but that character is not found before the end of the line . This results
in stopping the program execution and throwing a syntax error.

The `SyntaxError: EOL while scanning string literal` error when::

1. A closing quote is missing, for example::

      def printMsg():
          return "This is a test
      printMsg()

   output::

      return "This is a test
                         ^
      SyntaxError: EOL while scanning string literal

2. A string spans multiple lines, for example::

      def printMsg():
          str = "This is
            a test"
          print(str)
      printMsg()

The solution to the 2nd case is to use triple quotes::

   def printMsg():
       str = """This is
         a test"""
       print(str)
   printMsg()
