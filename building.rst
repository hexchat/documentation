Building
========

Windows
-------

Software
~~~~~~~~

Download and install (in their default install paths):

-  `Visual Studio 2013 Express for Windows Desktop <http://www.microsoft.com/visualstudio/eng/2013-downloads#d-2013-express>`_
-  `Inno Setup 5.5 Unicode <http://www.jrsoftware.org/isdl.php>`_
-  `Inno Download Plugin <http://dl.hexchat.net/misc/idpsetup-1.1.2.exe>`_
-  `7-Zip <http://7-zip.org/>`_
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

-  `Perl 5.18.2 x86`_ (extract to *c:\\mozilla-build\\perl-5.18\\Win32*)
-  Python_ 2.7 x86 (install to *c:\\mozilla-build\\python-2.7\\Win32*)
-  Python_ 3.4 x86 (install to *c:\\mozilla-build\\python-3.4\\Win32*)

-  `Perl 5.18.2 x64`_ (extract to *c:\\mozilla-build\\perl-5.18\\x64*)
-  Python_ 2.7 x64 (install to *c:\\mozilla-build\\python-2.7\\x64*)
-  Python_ 3.4 x64 (install to *c:\\mozilla-build\\python-3.4\\x64*)

.. _Perl 5.18.2 x86: http://dl.hexchat.net/misc/perl/perl-5.18.2-x86.7z
.. _Perl 5.18.2 x64: http://dl.hexchat.net/misc/perl/perl-5.18.2-x64.7z
.. _Python: http://www.python.org/download/

You can use other paths, but then you must update the related properties
in *win32\\hexchat.props* accordingly.

Building
~~~~~~~~

Open PowerShell as administrator and run::

	Set-ExecutionPolicy RemoteSigned

- If you're on 32-bit Windows, this is *C:\\Windows\\System32\\WindowsPowerShell\\v1.0\\powershell.exe*
- If you're on 64-bit Windows, this is *C:\\Windows\\SysWOW64\\WindowsPowerShell\\v1.0\\powershell.exe* (notice that this is the 32-bit PowerShell executable)

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
- apt: apt-get build-dep hexchat && apt-get install build-essential gnome-common
- apt (Older Ubuntu): apt-get build-dep xchat && apt-get install libnotify-dev libproxy-dev libpci-dev libcanberra-dev build-essential gnome-common

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

This will compile with defaults. See ``./configure --help`` for more info
about flags.

Building Theme Manager
~~~~~~~~~~~~~~~~~~~~~~

The theme manager isn't built by default on Unix. To do so get hexchat's source as mentioned above and install MonoDevelop with your package manager of choice then run this:

.. code-block:: bash

	./configure --with-theme-manager
	make && sudo make install

OS X
----

Homebrew
~~~~~~~~

HexChat can be installed via Homebrew. The Homebrew method is fairly
simple and is automatically configured to support Python and Perl so that
scripts can be loaded from ~/.config/hexchat/addons. First install
Homebrew_, then do the following:

.. code-block:: bash

	brew install hexchat
	# or to build directly from git:
	# brew install --HEAD hexchat
	
	# then launch it
	/usr/local/bin/hexchat

.. _Homebrew: http://brew.sh/

JHBuild
~~~~~~~

JHBuild is the alternative method; this is how the *official* HexChat.app is built.
It uses the quartz backend as well as a more native theme.
It can be more hands on and complex than the automated Homebrew method.

1. Follow the instructions on Gnome's site for `Building on OSX`_
2. Follow the instructions on Gnome's site for `Bundling on OSX`_
3. With jhbuild install: gtk-quartz-engine and gtk-engines

.. Note::
	Some of these builds may fail and require dropping to a shell to fix them.
	Most are trivial but the packages are always changing so you are on your own.

Once everything is set up we can build hexchat:

.. code-block:: bash

	git clone https://github.com/hexchat/hexchat.git && cd hexchat
	./autogen.sh && ./configure --prefix=$PREFIX
	make && make install
	# At this point you can use hexchat, but if you want a package...
	cd osx && ./makebundle.sh

.. _Building on OSX: https://wiki.gnome.org/Projects/GTK%2B/OSX/Building
.. _Bundling on OSX: https://wiki.gnome.org/Projects/GTK%2B/OSX/Bundling
