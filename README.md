# HexChat documentation

[![Build Status](https://travis-ci.org/hexchat/documentation.png)](https://travis-ci.org/hexchat/documentation)


## Getting started

* Install [Python](http://www.python.org/) (tested with 2.7.x)
* Install [Python pip](http://www.pip-installer.org/en/latest/installing.html#alternative-installation-procedures)
* Install Sphinx: `pip install Sphinx`
* On Windows: `SET PATH=C:\Python27;C:\Python27\Scripts;%PATH%`
* Run `make html` to build the HTML files
* Run the Python webserver to test your changes: `make server`
  (http://localhost:8000/)


## Conventions

* 4 Spaces are used for indentation.
* Ordered or unordered lists use visual indentation, where any non-list
  subelement would use an indentation of a multiple of 4 again.

Example:

```rest
- a list item
- a list item
  with a visually indented continuation
- a list item with a sub-list
  
  - that is also visually indented
- a list item with a code block

    .. code-block:: none

        that is not visually indented
```
