Appearance
==========

Theme Files
-----------

We distribute themes as :file:`.hct` files. These are just simply ZIP archives that you can *extract* manually into your `config folder <settings.html#config-files>`_ with HexChat closed. Some themes can be found `here <http://dl.hexchat.net/themes/>`_.

On unix for example you can extract these with this command while HexChat is closed:

    unzip ~/Downloads/monokai.hct -d ~/.config/hexchat

The theme manager is an optional external application, which is included with the `installer <https://hexchat.github.io/downloads.html>`_ on Windows and `buildable <building.html#building-theme-manager>`_ on Unix, that simply helps install themes.

Theming
-------

Colors
~~~~~~

Colors are defined in :menuselection:`Settings --> Preferences --> Colors`. Text Colors set the palette for events to use. The rest like background color directly affect parts of the UI.

`mIRC <http://www.mirc.com/colors.html>`_ colors (00-15) are what you refer to when sending colored text over IRC for others to see and vice versa, because of this they should somewhat follow a set of standards so clients can agree 04 is red.

Local colors (16-31) are to be used by HexChat only and can be anything you wish, these are typically what you use in your `events <appearance.html#text-events>`_.

Text Events
~~~~~~~~~~~

Text events control the look of every event you see. They can be customized in :menuselection:`Settings --> Text Events` using these codes to format it:

- **%C<fg>,<bg>** Color code (e.g. %C02,00 is blue on grey)
- **%R** Reverse color
- **%U** Underlined text
- **%B** Bold text
- **%I** Italic text (2.10.0+)
- **%H** Hide text
- **%O** Normal text
- **%%** Escaped %
- **$t** Text separator (tab character)
- **$aXXX** Ascii value
- **$<num>** Event information

.. note::

    Always hit enter after editing a field.

Icons
~~~~~

HexChat comes with built in icons for the tray, user list, and channel tree (which can be disabled in Preferences). You can use `custom icons <http://dl.hexchat.net/themes/icons/>`_ by placing icons (16x16 recommended) in an :file:`icons` subdir, which may need to be created, within your `config folder <settings.html#config-files>`_. The icons must be named exactly as follows including file extensions:

- User List

  - ulist_netop.png
  - ulist_founder.png
  - ulist_owner.png
  - ulist_op.png
  - ulist_halfop.png
  - ulist_voice.png

- Channel Tree

  - tree_channel.png
  - tree_dialog.png
  - tree_server.png
  - tree_util.png

- Tray Icon

  - tray_fileoffer.png
  - tray_highlight.png
  - tray_message.png
  - hexchat.png

Gtk Theme
~~~~~~~~~

To customize more than just text color such as the window borders you must install a gtk theme.
Here are some simple instructions for using them on Windows.

The only requirement for themes on Windows is that they are for gtk2 and they use the Pixbuf engine. Themes
requiring other engines will work but not look correct. HexChat has also only shipped this engine
since 2.10.0.

Before starting you need to install HexChat with the *Gtk theme* option unchecked. This will avoid
the installer modifying your theme or it conflicting with your theme.

Themes can be either installed globally (changing all gtk2 applications) or to HexChat specifically.
The latter requires write permissions to HexChat's install dir (e.g. in Program Files). Global themes
are set in *%USERPROFILE%\\.gtkrc-2.0* but will not be covered here.

This example will use the `Vertex theme <https://github.com/horst3180/Vertex-theme>`_.
Which contains an unoffensive dark theme and *mostly* uses the Pixbuf engine. Zips are on the releases page.

1. Create the directory *<installdir>\\share\\themes\\vertex\\gtk-2.0*
2. Extract contents of *vertex-gtk\\gtk-2.0-dark\\* into this directory.
3. Create an empty file at *<installdir>\\etc\\gtk-2.0\\gtkrc* (with no file extension!)
4. Edit it and add ``gtk-theme-name = "vertex"``

Buttons, Menus, and Popups
--------------------------

Userlist Popup
~~~~~~~~~~~~~~

Popups are shown when you right click on a nickname, either in the userlist or in the main chat itself. These can be edited in :menuselection:`Settings --> Userlist Popup`

The Name column can take either just the name of the entry, *SUB*/*ENDSUB* for submenus, *SEP* for separators, and *TOGGLE* for toggleable options.
Suffix the name with *~GTK_ICON_NAME~* to show an icon (see `Gtk Stock Items <https://developer.gnome.org/gtk2/stable/gtk2-Stock-Items.html>`_) and prefix a character with *_* for keyboard shortcuts (e.g. N_ame will bind a).

The Command column can take any `command <commands.html>`_ with text formatted using the same codes as `text events <appearance.html#text-events>`_ and on top of that they also have their own codes:

- **%a** all selected nicks
- **%c** current channel
- **%h** selected nick's hostname
- **%m** machine info
- **%n** your nickname
- **%s** selected nickname
- **%t** time/date
- **%u** selected nick's account (2.9.6+)

As a sidenote the **gui_ulist_doubleclick** setting can run a command using these codes when double-clicking a nick in the userlist.

Userlist Buttons
~~~~~~~~~~~~~~~~

Buttons are shown below the userlist, can be edited in :menuselection:`Settings --> Userlist Buttons`, and take the same syntax as `Userlist Popup <appearance.html#userlist-popup>`_ for commands.

Usermenu
~~~~~~~~

In order to add custom entries to your menu you need to first enable the usermenu with the command :command:`/set gui_usermenu on` which may require a restart. Once this is enabled you can go to :menuselection:`Usermenu --> Edit this Menu` to add any `command <commands.html>`_  you would like. For menu entries it supports the same as `Userlist Popups <appearance.html#userlist-popup>`_.
