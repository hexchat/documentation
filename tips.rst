Tips & Tricks
=============

Spell Check
-----------

Windows
~~~~~~~

HexChat for Windows uses MySpell for spelling via the Enchant library. The required libraries are included in the installer, but the dictionaries are big so they are distributed in a separate archive. They're from Debian Lenny.

.. image:: http://i.imgur.com/MxSpn7J.png

If you want to have spelling, do the following:

    1. Download the `Spelling Dictionaries <http://hexchat.org/downloads.html>`_ self-extracting archive
    2. Specify the languages you wish to have spelling for in :menuselection:`Settings --> Preferences --> Interface --> Input box`. You need to specify their language codes, see :file:`%LOCALAPPDATA%\\enchant\\myspell` for hints. By default, HexChat uses the LC_ALL environmental variable, or if it's unset, it falls back to en_US.

.. NOTE::
	For portable installs, you can use the :file:`share\\myspell\\dicts` subfolder instead of :file:`%LOCALAPPDATA%\\enchant\\myspell` (both will work, but the former one can be carried on a pendrive unlike the latter one).

Unix
~~~~

Install your spelling dictionaries via your package manager (something like myspell-en-us for English). Then make sure to enable spelling under :menuselection:`Settings --> Preferences --> Interface --> Input box`.

If you use static spelling (this is the default), also make sure to specify the languages you wish to have spelling for in :menuselection:`Settings --> Preferences --> Interface --> Input box`. You need to specify their language codes, separated by commas.

.. Note::
	Ubuntu users may have to manually install the libenchant-dev package for spelling to work.

Localization
------------

In order to start HexChat in a different language (for which a translation exists `here <https://www.transifex.com/projects/p/hexchat/>`_) you can use the regional settings of Windows, or set the LC_ALL user environmental variable. The value of the variable must be the two letter country code for your country. If in doubt, have a look at the share\locale folder. You have to restart HexChat for the changes to apply.

You can also use a batch file to affect only HexChat:

.. code-block:: bat

	@echo off
	set LC_ALL=en
	start hexchat.exe

This sets the language to English. You may use *fr* for French, *de* for German, etc. Save the code above as :file:`run.bat`, and copy it to the HexChat install folder. You can then start HexChat in the desired language by running the batch file.

Special Glyphs
--------------

There are many symbols which may not be supported by the main font you selected to use in HexChat, especially Asian glyphs and special characters, like a peace sign. In this case, you'll see "lego blocks" instead of them.

To circumvent this, you need to specify alternative fonts for glyphs not supported by your current font. You can specify them in :menuselection:`Settings --> Preferences --> Chatting --> Advanced --> Alternative fonts`. By default, it is set to *Arial Unicode MS, Lucida Sans Unicode, MS Gothic, Unifont*, which should fix most rendering errors.

`Unifont <http://unifoundry.com/unifont.html>`_ is freely available, so it is recommended to install it - it should solve most of your rendering problems.

In case you still get lego blocks, you'll need to add additional fonts to the list which support those obscure glyphs. Feel free to extend the list. You only need to specify font names, other info (such as size, weight, style etc.) should be omitted, otherwise those entries will be ignored. All font names must be separated by a comma and there mustn't be spaces before and/or after commas.

Please bear in mind that for some reason certain fonts that can display a certain glyph when used as the main font may not work when specified as an alternative font so you might have to play around it a bit.

Custom Certificates
-------------------

Custom Certificates allows you to identify to networks services using a certificate. Please do not mistake it for server certificate which allows you to connect to network with invalid certificate, right now Hexchat can't do it.

To use custom certificates you need to put your certificate file inside *certs* directory in Hexchat config dir which can be found here:

- Windows: %APPDATA%\HexChat
- Unix: ~/.config/hexchat

Certificate should be named after the network where it will be used, for example if you want to use it on *Rizon*, certificate file should look like this: *Rizon.pem*.

Note on Custom Server Certificates
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

On Windows it is possible to edit *cert.pem* file in Hexchat main installation directory and add custom certificate there. But this method isn't very effective as *cert.pem* is overwritten each time Hexchat installer is used.

Notice Placement
----------------

Other than channel messages and private messages, IRC has a notice type of message. This is intended to be used as a reply, something that will not cause the other client to send any acknowledgement back. When HexChat displays these messages, it shows them in a tab that it figures is appropriate.

Why replies from ChanServ may not appear in the current tab
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

When HexChat decides where to print a notice, it does so in the following order.

    1. In a query window you have with that user
    2. In the front tab, if the tab is a channel, the other user is on that channel, and you are on the correct network
    3. In the last joined channel you have in common with the other user
    4. The current tab, if you are on the same network
    5. The last tab you looked at that shares the correct network with the other user 

This means that if you issue a :command:`/cs info #yourchannel` from your channel, the reply may show up elsewhere if ChanServ isn't in your channel, but is in some other channel.

How to make notices show up in a consistent location
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The simplest method is always have notices in one location is to go to :menuselection:`Settings --> Preferences --> Channel switcher`, and check the Open an extra tab for server notices box. This will cause all server notices to go into one tab, and all user notices to go into another. In this case, ChanServ would be considered a user notice, and so will show up in the (notices) tab.

If you know who will notice you before hand, you can simply query the user before they notice you. This way, all notices from that user will show up in the query tab. In the case of ChanServ, this may allow an easier archive of commands you have done anyway.

If you want the notice to always go to the front tab (assuming you are looking at the network and you don't have a query open with the user), you can use Khisanth's `Notice to Front <http://lwsitu.com/xchat/notice_to_front.pl>`_ Perl script.

For other locations, a separate script would be required. While not currently implemented, it would be possible with a script to treat all notices like private messages (open a new query window when received), or place them in a specific existing tab, such as the server tab. At this point, the choice is up to you (or whoever designs the script).

Tor
---

1. Find a network that allows tor (most don't). Example: `freenode`_
2. Get tor working, this isn't a tor tutorial. Browser bundle is an easy way to test.
3. Set up proxy in :menuselection:`Settings --> Preferences --> Network Setup`. Example (with defaults):

.. image:: http://i.imgur.com/ohSfc.png

4. Setup the network in :menuselection:`HexChat --> Network List`. Note the ip from `freenode`_'s site (which may change) and for freenode SASL is required. Example:

.. image:: http://i.imgur.com/EMNx2.png

.. _freenode: http://freenode.net/irc_servers.shtml
