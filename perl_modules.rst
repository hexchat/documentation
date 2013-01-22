Building Perl modules on Windows
================================

Since version 2.9.5 on Windows you need to use specific Perl build for Hexchat for perl plugin to work. But this build doesn't have PPM and cpan doesn't work so if your perl script needs specific module to work you need to build it manually.


Software
--------

To start building perl modules you need to download and install this software (in their default install paths):

- `MozillaBuild`_ or `direct link`_ 
- `Visual Studio 2010 Professional Trial`_ 
- Perl `x86`_ or `x64`_ (Perl **MUST** be installed to C:\Perl)

.. _MozillaBuild: https://wiki.mozilla.org/MozillaBuild
.. _direct link: http://ftp.mozilla.org/pub/mozilla.org/mozilla/libraries/win32/MozillaBuildSetup-Latest.exe
.. _Visual Studio 2010 Professional Trial: http://www.microsoft.com/en-us/download/details.aspx?id=16057
.. _x86: https://github.com/downloads/hexchat/hexchat/Perl%205.16.2%20x86.msi
.. _x64: https://github.com/downloads/hexchat/hexchat/Perl%205.16.2%20x64.msi


Downloading and Extracting
--------------------------

Start MozillaBuild console (it's in C:\mozilla-build) *start-msvc10.bat* (if you're using Hexchat x86) or *start-msvc10-x64.bat* (if you're using Hexchat x64). 

Type: 

.. code-block:: none

 mkdir perl; cd perl 

(we will work in *perl* directory). 

All Perl modules can be downloaded from `cpan`_ site.

.. _cpan: http://search.cpan.org/

Now we can download the module which can be done using *wget http://link* command. 
Later we extract it using *tar -zxvf module.tar.gz*. 

In this guide we will build Net::Telnet module. So we are downloading it and extracting:

.. code-block:: none

 wget http://search.cpan.org/CPAN/authors/id/J/JR/JROGERS/Net-Telnet-3.03.tar.gz 
 tar -zxvf Net-Telnet-3.03.tar.gz 

Type *cd Net-Telnet-3.03* and we can start building.


Note on Perl module dependencies
--------------------------------

This building method isn't perfect so if you're building modue which depends on other module, you need to build it before repeating all steps in this guide.


Building
--------

Fist thing we must do is to configure module and point it to our perl installation using:

.. code-block:: none

 c:/perl/bin/perl.exe Makefile.pl 

Note: It really depends if it is Makefile.pl or Build.pl, just check in folder and read README.

You should get something like this:

.. code-block:: none

 Checking if your kit is complete... 
 Looks good 
 Writing Makefile for Net::Telnet 
 Writing MYMETA.yml and MYMETA.json 

It means that module was properly configured. Now we can move to compilation which can be done using:

.. code-block:: none

 nmake

After it we can install module using:

.. code-block:: none

 nmake install

Now if you didn't get any error you should have perl module installed and it can be used with perl script in Hexchat.


Whole operation should look like this:

.. figure:: http://i.imgur.com/2phxzxr.png
   :align: center