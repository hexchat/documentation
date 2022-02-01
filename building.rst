Building
========

Windows
-------

Software
~~~~~~~~

Download and install (in their default install paths):

- `Visual Studio 2019 Community <https://www.visualstudio.com/products/visual-studio-community-vs>`_
- `Inno Setup 5.5 Unicode <http://www.jrsoftware.org/isdl.php>`_
- `Inno Download Plugin <https://dl.hexchat.net/misc/idpsetup-1.5.1.exe>`_
- `7-Zip <http://7-zip.org/>`_
- `gendef <https://dl.hexchat.net/gtk-win32/gendef-20111031.7z>`_ (extract to *C:\\gtk-build*)
- `WinSparkle <https://dl.hexchat.net/gtk-win32/WinSparkle-20151011.7z>`_ (extract to *C:\\gtk-build\\WinSparkle*)


Source code
~~~~~~~~~~~

Download the `HexChat source code`_ and extract
it to somewhere. You will work in the extracted *hexchat* folder from
now.

.. _HexChat source code: https://github.com/hexchat/hexchat/zipball/master

GTK+
~~~~

Create a folder for GTK+, referred to as *YourDepsPath* from now (*C:\\gtk-build\\gtk* by default).
Specify the absolute path to *YourDepsPath* in *win32\\hexchat.props*
with the *YourDepsPath* property. Download:

- `GTK+ bundle <https://dl.hexchat.net/gtk>`_

Extract them to *Win32* and *x64* in *YourDepsPath*.

.. SEEALSO::

    If you would like to build GTK+ yourself, see this `repo <https://github.com/wingtk/gvsbuild>`_.

Language interfaces
~~~~~~~~~~~~~~~~~~~

You can skip this step, but then you won't be able to generate the
installer.
Download:

-  Perl_ 5.20 x86 (install to *C:\\gtk-build\\perl-5.20\\Win32*)
-  Python_ 2.7 x86 (install to *C:\\gtk-build\\python-2.7\\Win32*)
-  Python_ 3.6 x86 (install to *C:\\gtk-build\\python-3.6\\Win32*)

-  Perl_ 5.20 x64 (install to *C:\\gtk-build\\perl-5.20\\x64*)
-  Python_ 2.7 x64 (install to *C:\\gtk-build\\python-2.7\\x64*)
-  Python_ 3.6 x64 (install to *C:\\gtk-build\\python-3.6\\x64*)

.. _Perl: https://dl.hexchat.net/misc/perl/
.. _Python: http://www.python.org/download/

You can use other paths, but then you must update the related properties
in *win32\\hexchat.props* accordingly.

Building
~~~~~~~~

Open PowerShell as administrator and run::

    Set-ExecutionPolicy RemoteSigned -Scope CurrentUser

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

First of all, you have to install the build dependencies. Package names
differ across distros, so be creative and check meson's output
if you get an error.

Most package-managers can get the dependencies for you:

- dnf: dnf install meson 'dnf-command(builddep)' && dnf builddep hexchat && dnf install python3-cffi
- apt: apt-get install -y meson libcanberra-dev libdbus-glib-1-dev libglib2.0-dev libgtk2.0-dev libluajit-5.1-dev libpci-dev libperl-dev libssl-dev python3-dev python3-cffi desktop-file-utils

HexChat has its source code hosted using `Git <http://git-scm.com/>`_, so you have to install Git as
well. When it's ready, you can start the actual compilation, which is
basically:

.. code-block:: bash

    git clone https://github.com/hexchat/hexchat.git
    cd hexchat
    meson build
    ninja -C build
    sudo ninja -C build install

This will compile with defaults. See ``meson configure build`` for more info
about flags.

Building Theme Manager
~~~~~~~~~~~~~~~~~~~~~~

The theme manager isn't built by default on Unix. To do so get hexchat's source as mentioned above and install MonoDevelop with your package manager of choice then run this:

.. code-block:: bash

    meson configure build -Dtheme-manager=true
    ninja -C build
    sudo ninja -C build install

OS X
----

.. warning::

    macOS is not officially supported, is not simple to build for, and the end result will not be great.

JHBuild
~~~~~~~

JHBuild allows you to build the entire stack required by HexChat.
It uses the quartz backend as well as a more native theme.
It can be more hands on and complex than the automated Homebrew method.

1. Follow the instructions on Gnome's site for `Building on OSX`_

.. note::

    Some of these builds may fail and require dropping to a shell to fix them.
    Most are trivial but the packages are always changing so you are on your own.

Once everything is set up we can build hexchat:

.. code-block:: bash

    git clone https://github.com/hexchat/hexchat.git && cd hexchat
    ./autogen.sh --prefix=$JHBUILD_PREFIX
    make && make install
    hexchat

If you want to make the HexChat.app bundle it will take some more work.

1. Follow the instructions on Gnome's site for `Bundling on OSX`_
2. Build and install these packages

   - Modified `gtk-quartz-engine <https://github.com/TingPing/gtk-quartz-engine/tree/mono>`_
   - `xamarin-gtk-theme <https://github.com/mono/xamarin-gtk-theme>`_
   - enchant with these `patches <https://gist.github.com/TingPing/2d88a875b50da15c352d>`_

.. code-block:: bash

    cd osx && ./makebundle.sh

.. _Building on OSX: https://wiki.gnome.org/Projects/GTK%2B/OSX/Building
.. _Bundling on OSX: https://wiki.gnome.org/Projects/GTK%2B/OSX/Bundling
