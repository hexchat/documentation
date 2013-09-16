Building
========

Windows
-------

Software
~~~~~~~~

Download and install (in their default install paths):

-  `Visual Studio 2013 Express for Windows Desktop <http://www.microsoft.com/visualstudio/eng/2013-downloads#d-2013-express>`_
-  `Inno Setup 5.5 Unicode <http://www.jrsoftware.org/isdl.php>`_ 
-  `7-Zip x64 <http://7-zip.org/>`_ 
-  `gendef <http://dl.hexchat.net/gtk-win32/gendef-20111031.7z>`_ (extract to *c:\\mozilla-build*)
-  `msgfmt <http://dl.hexchat.net/gtk-win32/msgfmt-0.18.1.7z>`_ (extract to *c:\\mozilla-build*)


Source code
~~~~~~~~~~~

Download the `HexChat source code`_ and extract
it to somewhere. You will work in the extracted *hexchat* folder from
now.

.. _HexChat source code: https://github.com/hexchat/hexchat/zipball/master

GTK+
~~~~

Create a folder for GTK+, referred to as *YourDepsPath* from now (*C:\\mozilla-build\\hexchat\\gtk* by default).
Specify the absolute path to *YourDepsPath* in *win32\\hexchat.props*
with the *YourDepsPath* property. Download:

-  `GTK+ x86 bundle`_ 
-  `GTK+ x64 bundle`_ 

.. _GTK+ x86 bundle: http://dl.hexchat.net/gtk-win32/vc12/x86/gtk-x86.7z
.. _GTK+ x64 bundle: http://dl.hexchat.net/gtk-win32/vc12/x64/gtk-x64.7z

Extract them to *win32* and *x64* in *YourDepsPath*.

.. SEEALSO::
   If you would like to build GTK+ yourself, read this `guide <http://hexchat.github.io/gtk-win32/>`_.

Language interfaces
~~~~~~~~~~~~~~~~~~~

You can skip this step, but then you won't be able to generate the
installer.
Download:

-  `Perl 5.18 x86`_ (extract to *c:\\mozilla-build\\perl-5.18\\Win32*)
-  Python_ 2.7 x86 (install to *c:\\mozilla-build\\python-2.7\\Win32*)
-  Python_ 3.3 x86 (install to *c:\\mozilla-build\\python-3.3\\Win32*)

-  `Perl 5.18 x64`_ (extract to *c:\\mozilla-build\\perl-5.18\\x64*)
-  Python_ 2.7 x64 (install to *c:\\mozilla-build\\python-2.7\\x64*)
-  Python_ 3.3 x64 (install to *c:\\mozilla-build\\python-3.3\\x64*)

.. _Perl 5.18 x86: http://dl.hexchat.net/misc/perl/perl-5.18.0-x86.7z
.. _Perl 5.18 x64: http://dl.hexchat.net/misc/perl/perl-5.18.0-x64.7z
.. _Python: http://www.python.org/download/

You can use other paths, but then you must update the related properties
in *win32\\hexchat.props* accordingly.

Building
~~~~~~~~

Open *win32\\hexchat.sln*, right click on the *release/installer* (or
*release/copy* if you skipped the language interfaces) project and set
it as the startup project. Now you can compile from under the *Build*
menu to your taste.

If everything went fine, the resulting binaries will be found in
*hexchat-build\\Win32* and/or *hexchat-build\\x64*. It was easy, wasn't
it?

Unix
----

First of all, you have to install the build dependencies just like you
would for an XChat compilation. Package names differ across distros, so
be creative and check your *configure* output if you get an error. 
Also most package-managers can get the dependencies for you:

- yum: yum-builddep hexchat
- apt: apt-get build-dep xchat (and these... libnotify-dev libproxy-dev libpci-dev libcanberra-dev)

HexChat has its source code hosted using `Git <http://git-scm.com/>`_, so you have to install Git as
well. When it's ready, you can start the actual compilation, which is
basically:

.. code-block:: bash 

 git clone https://github.com/hexchat/hexchat.git 
 cd hexchat 
 ./autogen.sh 
 ./configure 
 make 
 sudo make install

This will compile with defaults. See *./configure --help* for more info
about flags.

Building Theme Manager
~~~~~~~~~~~~~~~~~~~~~~

The theme manager isn't built by default on Unix. To do so install MonoDevelop with your package manager of choice then run this:

.. code-block:: bash

	cd hexchat/src/htm
	mdtool --verbose build htm-mono.csproj
	mono thememan.exe

Mac
---

Install Homebrew_, then install all
the build dependencies of HexChat such as GTK+. Be creative and check
your *configure* output if you get an error.

.. _Homebrew: http://mxcl.github.com/homebrew/

Download the `testing package`_ which is prepared for Homebrew compilation (basically a clone of Git
HEAD with *./autogen.sh* ran on Debian 6). Extract it and run the
following commands:

.. _testing package: http://dl.hexchat.org/hexchat/osx/hexchat-2.9.4-mac.tar.gz

.. code-block:: bash

 cd hexchat 
 ./configure --disable-nls --disable-xlib --disable-perl 
 make 
 ./src/fe-gtk/hexchat 

See *./configure --help* for more info about flags.

