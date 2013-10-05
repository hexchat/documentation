Plugin Interface
================

.. default-domain:: c

Introduction
------------

Plugins for HexChat are written in C. The interface aims to keep 100%
binary compatability. This means that if you upgrade HexChat, you will
not need to recompile your plugins, they'll continue to work. The
interface doesn't depend on any structures and offsets, so compiler
versions shouldn't have an impact either. The only real requirement of a
HexChat plugin is that it define an *hexchat\_plugin\_init* symbol. This
is your entry point function, see the example below. You should make all
your global variables and functions *static*, so that a symbol is not
exported. There is no harm in exporting these symbols, but they are not
necessary and only pollute the name-space. Plugins are compiled as
shared objects (.so files), for example:

Most UNIX systems:

	 gcc -Wl --export-dynamic -Wall -O1 -shared -fPIC myplugin.c -o myplugin.so

OS X:

	 gcc -no-cpp-precomp -g -O2 -Wall -bundle -flat\_namespace -undefined suppress -o myplugin.so myplugin.c

See the `Windows section <plugins.html#plugins-on-windows-win32>`_ on how to compile a plugin using Visual Studio.

All strings passed to and from plugins are encoded in UTF-8, regardless
of locale.

Sample plugin
-------------

This simple plugin auto-ops anyone who joins a channel you're in. It
also adds a new command */AUTOOPTOGGLE*, which can be used to turn the
feature ON or OFF. Every HexChat plugin must define an
*hexchat\_plugin\_init* function, this is the normal entry point.
*hexchat\_plugin\_deinit* is optional.

.. code-block:: c

	 #include "hexchat-plugin.h"

	 #define PNAME "AutoOp"
	 #define PDESC "Auto Ops anyone that joins"
	 #define PVERSION "0.1"

	 static hexchat_plugin *ph;      /* plugin handle */
	 static int enable = 1;

	 static int
	 join_cb (char *word[], void *userdata)
	 {
			 if (enable)
			 {
					 /* Op ANYONE who joins */
					 hexchat_commandf (ph, "OP %s", word[1]);
			 }
			 /* word[1] is the nickname, as in the Settings->Text Events window in HexChat */

			 return HEXCHAT_EAT_NONE;        /* don't eat this event, HexChat needs to see it! */
	 }

	 static int
	 autooptoggle_cb (char *word[], char *word_eol[], void *userdata)
	 {
			 if (!enable)
			 {
					 enable = 1;
					 hexchat_print (ph, "AutoOping now enabled!\n");
			 }
			 else
			 {
					 enable = 0;
					 hexchat_print (ph, "AutoOping now disabled!\n");
			 }

			 return HEXCHAT_EAT_ALL;     /* eat this command so HexChat and other plugins can't process it */
	 }

	 void
	 hexchat_plugin_get_info (char **name, char **desc, char **version, void **reserved)
	 {
			 *name = PNAME;
			 *desc = PDESC;
			 *version = PVERSION;
	 }

	 int
	 hexchat_plugin_init (hexchat_plugin *plugin_handle, char **plugin_name, char **plugin_desc, char **plugin_version, char *arg)
	 {
			 /* we need to save this for use with any hexchat_* functions */
			 ph = plugin_handle;

			 /* tell HexChat our info */
			 *plugin_name = PNAME;
			 *plugin_desc = PDESC;
			 *plugin_version = PVERSION;

			 hexchat_hook_command (ph, "AutoOpToggle", HEXCHAT_PRI_NORM, autooptoggle_cb, "Usage: AUTOOPTOGGLE, Turns OFF/ON Auto Oping", 0);
			 hexchat_hook_print (ph, "Join", HEXCHAT_PRI_NORM, join_cb, 0);

			 hexchat_print (ph, "AutoOpPlugin loaded successfully!\n");

			 return 1;       /* return 1 for success */
	 }

What's *word* and *word\_eol*?
------------------------------

They are arrays of strings. They contain the parameters the user entered
for the particular command. For example, if you executed:

.. raw:: html

	 <pre>
	 /command NICK hi there

	 word[1] is command
	 word[2] is NICK
	 word[3] is hi
	 word[4] is there

	 word_eol[1] is command NICK hi there
	 word_eol[2] is NICK hi there
	 word_eol[3] is hi there
	 word_eol[4] is there
	 </pre>

These arrays are simply provided for your convenience. You are **not**
allowed to alter them. Both arrays are limited to 32 elements (index
31). *word[0]* and *word\_eol[0]* are reserved and should not be read.

Lists and Fields
----------------

Lists of information (DCCs, Channels, User list, etc.) can be retreived
with *hexchat\_list\_get*. All fields are **read only** and must be
copied if needed for a long time after calling *hexchat\_list\_str*. The
types of lists and fields available are:


+--------------+--------------------------------------------------------------------+--------+
| "channels"   | list of channels, querys and their servers                                  |
+--------------+--------------------------------------------------------------------+--------+
| Name         | Description                                                        | Type   |
+==============+====================================================================+========+
| channel      | Channel or query name                                              | string |
+--------------+--------------------------------------------------------------------+--------+
| channelkey   | Channels key or NULL (2.9.6+)                                      | string |
+--------------+--------------------------------------------------------------------+--------+
| chantypes    | Channel types e.g. “#!&”                                           | string |
+--------------+--------------------------------------------------------------------+--------+
| context      | (hexchat_context \*) pointer. Can be used with hexchat_set_context | string |
+--------------+--------------------------------------------------------------------+--------+
| flags        | - 1 = Connected                                                    | int    |
|              | - 2 = Connecting                                                   |        |
|              | - 4 = Marked away                                                  |        |
|              | - 8 = End of MOTD                                                  |        |
|              | - 16 = Has WHOX                                                    |        |
|              | - 32 = Has IDMSG                                                   |        |
|              | - 64 = Hide Join/Parts                                             |        |
|              | - 128 = unused                                                     |        |
|              | - 256 = Beep on Message                                            |        |
|              | - 512 = Blink Tray                                                 |        |
|              | - 1024 = Blink Taskbar                                             |        |
+--------------+--------------------------------------------------------------------+--------+
| id           | Unique server ID                                                   | int    |
+--------------+--------------------------------------------------------------------+--------+
| lag          | Lag in milliseconds                                                | int    |
+--------------+--------------------------------------------------------------------+--------+
| maxmodes     | Maximum modes per line                                             | int    |
+--------------+--------------------------------------------------------------------+--------+
| network      | Maximum modes per line                                             | int    |
+--------------+--------------------------------------------------------------------+--------+
| nickprefixes | Nickname prefixes e.g. “@+”                                        | string |
+--------------+--------------------------------------------------------------------+--------+
| nickmodes    | Nickname mode chars e.g. “ov”                                      | string |
+--------------+--------------------------------------------------------------------+--------+
| queue        | Number of bytes in the send-queue                                  | int    |
+--------------+--------------------------------------------------------------------+--------+
| server       | Server name to which this channel belongs                          | string |
+--------------+--------------------------------------------------------------------+--------+
| type         | - 1 = Server                                                       | int    |
|              | - 2 = Channel                                                      |        |
|              | - 3 = Dialog                                                       |        |
|              | - 4 = Notice                                                       |        |
|              | - 5 = SNotice                                                      |        |
+--------------+--------------------------------------------------------------------+--------+
| users        | Number of users in this channel                                    | int    |
+--------------+--------------------------------------------------------------------+--------+


+------------+----------------------------------------------------------------------+--------+
| "dcc"      | list of DCC file transfers                                                    |
+------------+----------------------------------------------------------------------+--------+
| Name       | Description                                                          | Type   |
+============+======================================================================+========+
| address32  | Address of the remote user (ipv4 address)                            | int    |
+------------+----------------------------------------------------------------------+--------+
| cps        | Bytes per second (speed)                                             | int    |
+------------+----------------------------------------------------------------------+--------+
| destfile   | Destination full pathname                                            | string |
+------------+----------------------------------------------------------------------+--------+
| file       | File name                                                            | string |
+------------+----------------------------------------------------------------------+--------+
| nick       | Nickname of person who the file is from/to                           | string |
+------------+----------------------------------------------------------------------+--------+
| port       | TCP port number                                                      | int    |
+------------+----------------------------------------------------------------------+--------+
| pos        | Bytes sent/received                                                  | int    |
+------------+----------------------------------------------------------------------+--------+
| poshigh    | Bytes sent/received, high order 32 bits                              | int    |
+------------+----------------------------------------------------------------------+--------+
| resume     | Point at which this file was resumed (or zero if it was not resumed) | int    |
+------------+----------------------------------------------------------------------+--------+
| resumehigh | Point at which this file was resumed, high order 32 bits             | int    |
+------------+----------------------------------------------------------------------+--------+
| size       | File size in bytes, low order 32 bits (cast it to unsigned)          | int    |
+------------+----------------------------------------------------------------------+--------+
| sizehigh   | File size in bytes, high order 32 bits                               | int    |
+------------+----------------------------------------------------------------------+--------+
| status     | - 0 = Queued                                                         | int    |
|            | - 1 = Active                                                         |        |
|            | - 2 = Failed                                                         |        |
|            | - 3 = Done                                                           |        |
|            | - 4 = Connecting                                                     |        |
|            | - 5 = Aborted                                                        |        |
+------------+----------------------------------------------------------------------+--------+
| type       | - 0 = Send                                                           | int    |
|            | - 1 = Recieve                                                        |        |
|            | - 1 = ChatRecv                                                       |        |
|            | - 1 = ChatSend                                                       |        |
+------------+----------------------------------------------------------------------+--------+


+----------+----------------------------------------------+--------+
| "ignore" | current ignore list                                   |
+----------+----------------------------------------------+--------+
| Name     | Description                                  | Type   |
+==========+==============================================+========+
| mask     | Ignore mask. .e.g. \*!\*@\*.aol.com          | string |
+----------+----------------------------------------------+--------+
| flags    | - 0 = Private                                | int    |
|          | - 1 = Notice                                 |        |
|          | - 2 = Channel                                |        |
|          | - 3 = CTCP                                   |        |
|          | - 4 = Invite                                 |        |
|          | - 5 = Unignore                               |        |
|          | - 6 = NoSave                                 |        |
|          | - 7 = DCC                                    |        |
+----------+----------------------------------------------+--------+


======== ================================================================== =======
"notify" list of people on notify
-------- --------------------------------------------------------------------------
Name     Description                                                        Type
======== ================================================================== =======
networks Networks to which this nick applies. Comma separated. May be NULL. string
nick     Nickname                                                           string
flags    Bit field of flags. 0=Is online.                                   int
on       Time when user came online.                                        time\_t
off      Time when user went offline.                                       time\_t
seen     Time when user the user was last verified still online.            time\_t
======== ================================================================== =======


Fields are only valid for the context when hexchat\_list\_get() was
called (i.e. you get information about the user ON THAT ONE SERVER
ONLY). You may cycle through the "channels" list to find notify
information for every server.


========== ============================================================================================ ========
"users"    list of users in the current channel
---------- -----------------------------------------------------------------------------------------------------
Name       Description                                                                                  Type
========== ============================================================================================ ========
account    Account name or NULL (2.9.6+)                                                                string
away       Away status (boolean)                                                                        int
lasttalk   Last time the user was seen talking                                                          time\_t
nick       Nick name                                                                                    string
host       Host name in the form: user@host (or NULL if not known).                                     string
prefix     Prefix character, .e.g: @ or +. Points to a single char.                                     string
realname   Real name or NULL                                                                            string
selected   Selected status in the user list, only works for retrieving the user list of the focused tab int
========== ============================================================================================ ========


Example:

.. code-block:: c

			 list = hexchat_list_get (ph, "dcc");

			 if (list)
			 {
					 hexchat_print (ph, "--- DCC LIST ------------------\nFile  To/From   KB/s   Position\n");

					 while (hexchat_list_next (ph, list))
					 {
							 hexchat_printf (ph, "%6s %10s %.2f  %d\n",
									 hexchat_list_str (ph, list, "file"),
									 hexchat_list_str (ph, list, "nick"),
									 hexchat_list_int (ph, list, "cps") / 1024,
									 hexchat_list_int (ph, list, "pos"));
					 }

					 hexchat_list_free (ph, list);
			 }

Plugins on Windows (Win32)
--------------------------

All you need is Visual Studio setup as explained in
`Building <http://docs.hexchat.org/en/latest/building.html>`_. Your best bet
is to use an existing plugin (such as the currently unused SASL plugin)
in the HexChat solution as a starting point. You should have the
following files:

-  `hexchat-plugin.h <https://github.com/hexchat/hexchat/blob/master/src/common/hexchat-plugin.h>`_
	 - main plugin header
-  plugin.c - Your plugin, you need to write this one :)
-  plugin.def - A simple text file containing the following:

.. raw:: html

	 <pre>
			 EXPORTS
			 hexchat_plugin_init
			 hexchat_plugin_deinit
			 hexchat_plugin_get_info
	 </pre>

Leave out *hexchat\_plugin\_deinit* if you don't intend to define that
function. Then compile your plugin in Visual Studio as usual.

**Caveat:** plugins compiled on Win32 **must** have a global variable
called *ph*, which is the *plugin\_handle*, much like in the sample
plugin above.

Controlling the GUI
-------------------

A simple way to perform basic GUI functions is to use the */GUI*
command. You can execute this command through the input box, or by
calling *hexchat\_command (ph, "GUI .....");*.

-  **GUI ATTACH:** Same function as "Attach Window" in the HexChat menu.
-  **GUI DETACH:** Same function as "Detach Tab" in the HexChat menu.
-  **GUI APPLY:** Similar to clicking OK in the settings window. Execute
	 this after /SET to activate GUI changes.
-  **GUI COLOR *n*:** Change the tab color of the current context, where
	 n is a number from 0 to 3.
-  **GUI FOCUS:** Focus the current window or tab.
-  **GUI FLASH:** Flash the taskbar button. It will flash only if the
	 window isn't focused and will stop when it is focused by the user.
-  **GUI HIDE:** Hide the main HexChat window completely.
-  **GUI ICONIFY:** Iconify (minimize to taskbar) the current HexChat
	 window.
-  **GUI MSGBOX *text*:** Displays a asynchronous message box with your
	 text.
-  **GUI SHOW:** Show the main HexChat window (if currently hidden).

You can add your own items to the menu bar. The menu command has this
syntax:

.. raw:: html

	 <pre>
			 MENU [-eX] [-i&lt;ICONFILE>] [-k&lt;mod>,&lt;key>] [-m] [-pX] [-rX,group] [-tX] {ADD|DEL} &lt;path> [command] [unselect command]
	 </pre>

For example:

.. raw:: html

	 <pre>
			 MENU -p5 ADD FServe
			 MENU ADD "FServe/Show File List" "fs list"
			 MENU ADD FServe/-
			 MENU -k4,101 -t1 ADD "FServe/Enabled" "fs on" "fs off"
			 MENU -e0 ADD "FServe/Do Something" "fs action"
	 </pre>

In the example above, it would be recommended to execute *MENU DEL
FServe* inside your *hexchat\_plugin\_deinit* function. The special item
with name "-" will add a separator line.

Parameters and flags:

-  **-eX:** Set enable flag to X. -e0 for disable, -e1 for enable. This
	 lets you create a disabled (shaded) item.
-  **-iFILE:** Use an icon filename FILE. Not supported for toggles or
	 radio items.
-  **-k<mod>,<key>:** Specify a keyboard shortcut. "mod" is the modifier
	 which is a bitwise OR of: 1-SHIFT 4- CTRL 8-ALT in decimal. "key" is
	 the key value in decimal, e.g. -k5,101 would specify SHIFT-CTRL-E.
-  **-m:** Specify that this label should be treated as Pango Markup
	 language. Since forward slash ("/") is already used in menu paths,
	 you should replace closing tags with an ASCII 003 instead e.g.:
	 hexchat\_command (ph, "MENU -m ADD "<b>Bold Menu<03b>"");
-  **-pX:** Specify a menu item's position number. e.g. -p5 will cause
	 the item to be inserted in the 5th place. If the position is a
	 negative number, it will be used as an offset from the
	 bottom/right-most item.
-  **-rX,group:** Specify a radio menu item, with initial state X and a
	 group name. The group name should be the exact label of another menu
	 item (without the path) that this item will be grouped with. For
	 radio items, only a select command will be executed (no unselect
	 command).
-  **-tX:** Specify a toggle menu item with an initial state. -t0 for an
	 "unticked" item and -t1 for a "ticked" item.

If you want to change an item's toggle state or enabled flag, just *ADD*
an item with exactly the same name and command and specify the *-tX -eX*
parameters you need.

It's also possible to add items to HexChat's existing menus, for
example:

.. raw:: html

	 <pre>
			 MENU ADD "Settings/Sub Menu"
			 MENU -t0 ADD "Settings/Sub Menu/My Setting" myseton mysetoff
	 </pre>

However, internal names and layouts of HexChat's menu may change in the
future, so use at own risk.

Here is an example of Radio items:

.. raw:: html

	 <pre>
			 MENU ADD "Language"
			 MENU -r1,"English" ADD "Language/English" cmd1
			 MENU -r0,"English" ADD "Language/Spanish" cmd2
			 MENU -r0,"English" ADD "Language/German" cmd3
	 </pre>

You can also change menus other than the main one (i.e popup menus).
Currently they are:

============ ============================================================
Root Name    Menu
============ ============================================================
$TAB         Tab menu (right click a channel/query tab or treeview row)
$TRAY        System Tray menu
$URL         URL link menu
$NICK        Userlist nick-name popup menu
$CHAN        Menu when clicking a channel in the text area
============ ============================================================

Example:

.. raw:: html

	 <pre>
			 MENU -p0 ADD "$TAB/Cycle Channel" cycle
	 </pre>

You can manipulate HexChat's system tray icon using the */TRAY* command:

.. raw:: html

	 <pre>
			 Usage:
			 TRAY -f &lt;timeout> &lt;file1> [&lt;file2>] Flash tray between two icons. Leave off file2 to use default HexChat icon.
			 TRAY -f &lt;filename>                  Set tray to a fixed icon.
			 TRAY -i &lt;number>                    Flash tray with an internal icon.
			 TRAY -t &lt;text>                      Set the tray tooltip.
			 TRAY -b &lt;title> &lt;text>              Set the tray balloon.
	 </pre>

Icon numbers:

-  2: Message
-  5: Highlight
-  8: Private
-  11:File

For tray balloons on Linux, you'll need libnotify.

Filenames can be *ICO* or *PNG* format. *PNG* format is supported on
Linux/BSD and Windows XP. Set a timeout of -1 to use HexChat's default.

Handling UTF-8/Unicode strings
------------------------------

The HexChat plugin API specifies that strings passed to and from HexChat
must be encoded in UTF-8.

What does this mean for the plugin programmer? You just have to be a
little careful when passing strings obtained from IRC to system calls.
For example, if you're writing a file-server bot, someone might message
you a filename. Can you pass this filename directly to open()? Maybe! If
you're lazy... The correct thing to do is to convert the string to
"system locale encoding", otherwise your plugin will fail on non-ascii
characters.

Here are examples on how to do this conversion on Unix and Windows. In
this example, someone will CTCP you the message "SHOWFILE <filename>".

.. code-block:: c

	 static int
	 ctcp_cb (char *word[], char *word_eol[], void *userdata)
	 {
			 if(strcmp(word[1], "SHOWFILE") == 0)
			 {
					 get_file_name (nick, word[2]);
			 }

			 return HEXCHAT_EAT_HEXCHAT;
	 }

	 static void
	 get_file_name (char *nick, char *fname)
	 {
			 char buf[256];
			 FILE *fp;

			 /* the fname is in UTF-8, because it came from the HexChat API */

	 #ifdef _WIN32

			 wchar_t wide_name[MAX_PATH];

			 /* convert UTF-8 to WIDECHARs (aka UTF-16LE) */
			 if (MultiByteToWideChar (CP_UTF8, 0, fname, -1, wide_name, MAX_PATH) &lt; 1)
			 {
					 return;
			 }

			 /* now we have WIDECHARs, so we can _wopen() or CreateFileW(). */
			 /* _wfopen actually requires NT4, Win2000, XP or newer. */
			 fp = _wfopen (wide_name, "r");

	 #else

			 char *loc_name;

			 /* convert UTF-8 to System Encoding */
			 loc_name = g_filename_from_utf8 (fname, -1, 0, 0, 0);
			 if(!loc_name)
			 {
					 return;
			 }

			 /* now open using the system's encoding */
			 fp = fopen (loc_name, "r");
			 g_free (loc_name);

	 #endif

			 if (fp)
			 {
					 while (fgets (buf, sizeof (buf), fp))
					 {
							 /* send every line to the user that requested it */
							 hexchat_commandf (ph, "QUOTE NOTICE %s :%s", nick, buf);
					 }
					 fclose (fp);
			 }
	 }

Types and Constants
-------------------

.. type:: hexchat_plugin
					hexchat_list
					hexchat_hook
					hexchat_context
					hexchat_event_attrs


.. var:: HEXCHAT_PRI_HIGHEST
				 HEXCHAT_PRI_HIGH
				 HEXCHAT_PRI_NORM
				 HEXCHAT_PRI_LOW
				 HEXCHAT_PRI_LOWEST

.. var:: HEXCHAT_EAT_NONE
				 HEXCHAT_EAT_XCHAT
				 HEXCHAT_EAT_PLUGIN
				 HEXCHAT_EAT_ALL

.. var:: HEXCHAT_FD_READ
				 HEXCHAT_FD_WRITE
				 HEXCHAT_FD_EXCEPTION
				 HEXCHAT_FD_NOTSOCKET


Functions
---------

General Functions
'''''''''''''''''

.. function:: void hexchat_command (hexchat_plugin *ph, const char *command)

	Executes a command as if it were typed in HexChat's input box.

	:param ph: Plugin handle (as given to :func:`hexchat_plugin_init`).
	:param command: Command to execute, without the forward slash "/".


.. function:: void hexchat_commandf (hexchat_plugin *ph, const char *format, ...)

	Executes a command as if it were typed in HexChat's
	input box and provides string formatting like :func:`printf`.

	:param ph: Plugin handle (as given to :func:`hexchat_plugin_init`).
	:param format: The format string.


.. function:: void hexchat_print (hexchat_plugin *ph, const char *text)

	Prints some text to the current tab/window.

	:param ph: Plugin handle (as given to :func:`hexchat_plugin_init`).
	:param text: Text to print. May contain mIRC color codes.


.. function:: void hexchat_printf (hexchat_plugin *ph, const char *format, ...)

	Prints some text to the current tab/window and provides formatting like :func:`printf`.

	:param ph: Plugin handle (as given to :func:`hexchat_plugin_init`).
	:param format: The format string.


.. function:: int hexchat_emit_print (hexchat_plugin *ph, const char *event_name, ...)

	Generates a print event. This can be any event found in
	the :menuselection:`Settings --> Text Events` window. The vararg parameter
	list **must** always be NULL terminated. Special care should be taken
	when calling this function inside a print callback (from
	:func:`hexchat_hook_print`), as not to cause endless recursion.


	:param ph: Plugin handle (as given to :func:`hexchat_plugin_init`).
	:param event_name: Text event to print.

	:returns: 0 on Failure, 1 on Success

	**Example:**

	.. code-block:: c

		 hexchat_emit_print (ph, "Channel Message", "John", "Hi there", "@", NULL);


.. function:: int hexchat_emit_print_attrs (hexchat_plugin *ph, hexchat_event_attrs *attrs, const char *event_name, ...)

	Generates a print event. This is the same as
	:func:`hexchat_emit_print` but it passes an :type:`hexchat_event_attrs *`
	to hexchat with the print attributes.


	:param ph: Plugin handle (as given to :func:`hexchat_plugin_init`).
	:param attrs: Print attributes. This should be obtained with :func:`hexchat_event_attrs_create` and freed with :func:`hexchat_event_attrs_free`.
	:param event_name: Text event to print.

	:returns: 0 on Failure, 1 on Success

	.. versionadded:: 2.9.6

	**Example:**

	.. code-block:: c

		 hexchat_event_attrs *attrs;

		 attrs = hexchat_event_attrs_create (ph);
		 attrs->server_time_utc = 1342224702;
		 hexchat_emit_print (ph, attrs, "Channel Message", "John", "Hi there", "@", NULL);
		 hexchat_event_attrs_free (ph, attrs);

.. function:: void hexchat_send_modes (hexchat_plugin *ph, const char *targets[], int ntargets, \
										int modes_per_line, char sign, char mode)

	Sends a number of channel mode changes to the current
	channel. For example, you can Op a whole group of people in one go. It
	may send multiple MODE lines if the request doesn't fit on one. Pass 0
	for *modes_per_line* to use the current server's maximum possible.
	This function should only be called while in a channel context.

	:param ph: Plugin handle (as given to :func:`hexchat_plugin_init`).
	:param targets: Array of targets (strings). The names of people whom the action will be performed on.
	:param ntargets: Number of elements in the array given.
	:param modes_per_line: Maximum modes to send per line.
	:param sign: Mode sign, '-' or '+'.
	:param mode: Mode char, e.g. 'o' for Ops.

	**Example:** (Ops the three names given)

	.. code-block:: c

		 const char *names_to_Op[] = {"John", "Jack", "Jill"};
		 hexchat_send_modes (ph, names_to_Op, 3, 0, '+', 'o');


.. function:: int hexchat_nickcmp (hexchat_plugin *ph, const char *s1, const char *s2)

	Performs a nick name comparision, based on the current
	server connection. This might be an RFC1459 compliant string compare, or
	plain ascii (in the case of DALNet). Use this to compare channels and
	nicknames. The function works the same way as :func:`strcasecmp`.

	:param ph: Plugin handle (as given to :func:`hexchat_plugin_init`).
	:param s1: String to compare.
	:param s2: String to compare *s1* to.

	**Quote from RFC1459:** >Because of IRC's scandanavian origin, the
	characters {}\| are considered to be the lower case equivalents of the
	characters [], respectively. This is a critical issue when determining
	the equivalence of two nicknames.

	:returns: An integer less than, equal to, or greater than zero if
		*s1* is found, respectively, to be less than, to match, or be greater than *s2*.

.. function:: char* hexchat_strip (hexchat_plugin *ph, const char *str, int len, int flags)

	Strips mIRC color codes and/or text attributes (bold,
	underlined etc) from the given string and returns a newly allocated
	string.

	:param ph: Plugin handle (as given to :func:`hexchat_plugin_init`).
	:param str: String to strip.
	:param len: Length of the string (or -1 for NULL terminated).
	:param flags: Bit-field of flags:
		 -  0: Strip mIRC colors.
		 -  1: Strip text attributes.

	:returns: A newly allocated string or NULL for failure. You must free this string with :func:`hexchat_free`.

	**Example:**

	.. code-block:: c

		 {
			 char *new_text;

			 /* strip both colors and attributes by using the 0 and 1 bits (1 BITWISE-OR 2) */
			 new_text = hexchat_strip (ph, "\00312Blue\003 \002Bold!\002", -1, 1 | 2);

			 if (new_text)
			 {
					 /* new_text should now contain only "Blue Bold!" */
					 hexchat_printf (ph, "%s\n", new_text);
					 hexchat_free (ph, new_text);
			 }
		 }


.. function:: void hexchat_free (hexchat_plugin *ph, void *ptr)

	Frees a string returned by **hexchat\_\*** functions.
	Currently only used to free strings from :func:`hexchat_strip`.

	:param ph: Plugin handle (as given to :func:`hexchat_plugin_init`).
	:param ptr: Pointer to free.


.. function:: hexchat_event_attrs *hexchat_event_attrs_create (hexchat_plugin *ph)

	Allocates a new :type:`hexchat_event_attrs`. The attributes are initially
	marked as unused.

	:returns: A pointer to the allocated :type:`hexchat_event_attrs`. Should be freed by :func:`hexchat_event_attrs_free`.

	.. versionadded:: 2.9.6

.. function:: void hexchat_event_attrs_free (hexchat_plugin *ph, hexchat_event_attrs *attrs)

	Frees an :type:`hexchat_event_attrs`.

	:param attrs: Attributes previously allocated by :func:`hexchat_event_attrs_create`.

	.. versionadded:: 2.9.6


Getting Information
'''''''''''''''''''

.. function:: const char* hexchat_get_info (hexchat_plugin *ph, const char *id)

	Returns information based on your current context.

	:param ph: Plugin handle (as given to :func:`hexchat_plugin_init`).
	:param id: ID of the information you want. List of ID's(case sensitive):

		 -  **away:** away reason or NULL if you are not away.
		 -  **channel:** current channel name.
		 -  **charset:** character-set used in the current context.
		 -  **configdir:** HexChat config directory, e.g.:
			``/home/user/.config/hexchat``. This string is encoded in UTF-8.
		 -  **event\_text <name>:** text event format string for *name*.
		 -  **gtkwin\_ptr:** (GtkWindow \*).
		 -  **host:** real hostname of the server you connected to.
		 -  **inputbox:** the input-box contents, what the user has typed.
		 -  **libdirfs:** library directory. e.g. /usr/lib/hexchat. The same
			directory used for auto-loading plugins. This string isn't
			necessarily UTF-8, but local file system encoding.
		 -  **modes:** channel modes, if known, or NULL.
		 -  **network:** current network name or NULL.
		 -  **nick:** your current nick name.
		 -  **nickserv:** nickserv password for this network or NULL.
		 -  **server:** current server name (what the server claims to be).
			NULL if you are not connected.
		 -  **topic:** current channel topic.
		 -  **version:** HexChat version number.
		 -  **win\_ptr:** native window pointer. Unix: (GtkWindow \*) Win32:
			HWND.
		 -  **win\_status:** window status: "active", "hidden" or "normal".

	:returns: A string of the requested information, or NULL. This string
		must not be freed and must be copied if needed after the call to :func:`hexchat_get_info`.


.. function:: int hexchat_get_prefs (hexchat_plugin *ph, const char *name, \
									const char **string, int *integer)

	Provides HexChat's setting information (that which is
	available through the :command:`/SET` command). A few extra bits of information
	are available that don't appear in the :command:`/SET` list, currently they are:

		-  **state\_cursor:** Current input box cursor position (characters, not
			 bytes).
		-  **id:** Unique server id

	:param ph: Plugin handle (as given to :func:`hexchat_plugin_init`).
	:param name: Setting name required.
	:param string: Pointer-pointer which to set.
	:param integer: Pointer to an integer to set, if setting is a boolean or integer type.

	:returns:
		-  0: Failed.
		-  1: Returned a string.
		-  2: Returned an integer.
		-  3: Returned a boolean.

	**Example:**

	.. code-block:: c

		 {
			 int i;
			 const char *str;

			 if (hexchat_get_prefs (ph, "irc_nick1", &amp;str, &amp;i) == 1)
			 {
					 hexchat_printf (ph, "Current nickname setting: %s\n", str);
			 }
		 }


.. function:: hexchat_list* hexchat_list_get (hexchat_plugin *ph, const char *name)

	Retreives lists of information.

	:param name: Name from the `List and Fields Table <plugins.html#lists-and-fields>`_
	:returns: hexchat_list to be used by the following functions.


.. function:: const char* const* hexchat_list_fields (hexchat_plugin *ph, const char *name)

	Lists fields in a given list.

	:param name: Name from the `List and Fields Table <plugins.html#lists-and-fields>`_


.. function:: int hexchat_list_next (hexchat_plugin *ph, hexchat_list *xlist)

	Selects the next list item in a list.

	:param xlist: :type:`hexchat_list` returned by :func:`hexchat_list_get`


.. function:: const char* hexchat_list_str (hexchat_plugin *ph, hexchat_list *xlist, const char *name)

	Gets a string field from a list.

	:param name: Name from the `List and Fields Table <plugins.html#lists-and-fields>`_
	:param xlist: :type:`hexchat_list` returned by :func:`hexchat_list_get`


.. function:: int hexchat_list_int (hexchat_plugin *ph, hexchat_list *xlist, const char *name)

	Gets a int field from a list.

	:param name: Name from the `List and Fields Table <plugins.html#lists-and-fields>`_
	:param xlist: :type:`hexchat_list` returned by :func:`hexchat_list_get`


.. function:: time_t hexchat_list_time (hexchat_plugin *ph, hexchat_list *xlist, const char *name)

	Gets a time field from a list.

	:param name: Name from the `List and Fields Table <plugins.html#lists-and-fields>`_
	:param xlist: :type:`hexchat_list` returned by :func:`hexchat_list_get`

.. function:: void hexchat_list_free (hexchat_plugin *ph, hexchat_list *xlist)

	Frees a list.

	:param xlist: :type:`hexchat_list` returned by :func:`hexchat_list_get`


Hook Functions
''''''''''''''

.. function:: hexchat_hook* hexchat_hook_command (hexchat_plugin *ph, const char *name, int pri, \
								int (*callb) (char *word[], char *word_eol[], void *user_data), \
								const char *help_text, void *userdata)

	Adds a new :command:`/command`. This allows your program to
	handle commands entered at the input box. To capture text without a "/"
	at the start (non-commands), you may hook a special name of "". i.e
	**hexchat_hook_command(ph, "", ...)**.

	Commands hooked that begin with a period ('.') will be hidden in :command:`/HELP` and :command:`/HELP -l`.

	:param ph: Plugin handle (as given to :func:`hexchat_plugin_init`).
	:param name: Name of the command (without the forward slash).
	:param pri: Priority of this command. Use :data:`HEXCHAT_PRI_NORM`.
	:param callb: Callback function. This will be called when the user executes the given command name.
	:param help_text: String of text to display when the user executes :command:`/HELP` for this command. May be NULL if you're lazy.
	:param userdata: Pointer passed to the callback function.

	:returns: Pointer to the hook. Can be passed to :func:`hexchat_unhook`.

	**Example:**

	.. code-block:: c

		 static int
		 onotice_cb (char *word[], char *word_eol[], void *userdata)
		 {
			 if (word_eol[2][0] == 0)
			 {
					 hexchat_printf (ph, "Second arg must be the message!\n");
					 return HEXCHAT_EAT_ALL;
			 }

			 hexchat_commandf (ph, "NOTICE @%s :%s", hexchat_get_info (ph, "channel"), word_eol[2]);
			 return HEXCHAT_EAT_ALL;
		 }

		 hexchat_hook_command (ph, "ONOTICE", HEXCHAT_PRI_NORM, onotice_cb, "Usage: ONOTICE &lt;message> Sends a notice to all ops", NULL);


.. function:: hexchat_hook* hexchat_hook_fd (hexchat_plugin *ph, int fd, int flags, \
											int (*callb) (int fd, int flags, void *user_data), void *userdata)

	Hooks a socket or file descriptor. WIN32: Passing a
	pipe from MSVCR71, MSVCR80 or other variations is not supported at this
	time.

	:param ph: Plugin handle (as given to *hexchat\_plugin\_init ()*).
	:param fd: The file descriptor or socket.
	:param flags: One or more of `HEXCHAT_FD_\* constants <plugins.html#types-and-constants>`_ tells HexChat that the
		provided *fd* is not a socket, but an "MSVCRT.DLL" pipe.
	:param callb: Callback function. This will be called when the socket is
		available for reading/writing or exception (depending on your chosen *flags*)
	:param userdata: Pointer passed to the callback function.

	:returns: Pointer to the hook. Can be passed to :func:`hexchat_unhook`.


.. function:: hexchat_hook* hexchat_hook_print (hexchat_plugin *ph, const char *name, int pri, \
												int (*callb) (char *word[], void *user_data), void *userdata)

	Registers a function to trap any print events. The
	event names may be any available in the :menuselection:`Settings --> Text Events` window.
	There are also some extra "special" events you may hook using this
	function. Currently they are:

	-  "Open Context": Called when a new hexchat\_context is created.
	-  "Close Context": Called when a hexchat\_context pointer is closed.
	-  "Focus Tab": Called when a tab is brought to front.
	-  "Focus Window": Called a toplevel window is focused, or the main
		 tab-window is focused by the window manager.
	-  "DCC Chat Text": Called when some text from a DCC Chat arrives. It
		 provides these elements in the *word[]* array:

		 .. raw:: html

			<pre>
					word[1] Address
					word[2] Port
					word[3] Nick
					word[4] The Message
			</pre>

	-  "Key Press": Called when some keys are pressed in the input box. It
		 provides these elements in the *word[]* array:

		 .. raw:: html

			<pre>
					word[1] Key Value
					word[2] State Bitfield (shift, capslock, alt)
					word[3] String version of the key
					word[4] Length of the string (may be 0 for unprintable keys)
			</pre>

	:param ph: Plugin handle (as given to :func:`hexchat_plugin_init`).
	:param name: Name of the print event (as in *Text Events* window).
	:param pri: Priority of this command. Use :data:`HEXCHAT_PRI_NORM`.
	:param callb: Callback function. This will be called when this event name is printed.
	:param userdata: Pointer passed to the callback function.

	:returns: Pointer to the hook. Can be passed to :func:`hexchat_unhook`.

	**Example:**

	.. code-block:: c

		 static int
		 youpart_cb (char *word[], void *userdata)
		 {
			 hexchat_printf (ph, "You have left channel %s\n", word[3]);
			 return HEXCHAT_EAT_HEXCHAT;     /* dont let HexChat do its normal printing */
		 }

		 hexchat_hook_print (ph, "You Part", HEXCHAT_PRI_NORM, youpart_cb, NULL);

.. function:: hexchat_hook* hexchat_hook_print_attrs (hexchat_plugin *ph, const char *name, int pri, \
		int (*callb) (char *word[], hexchat_event_attrs *attrs, void *user_data), void *userdata)

	Registers a function to trap any print events. This is the same as
	:func:`hexchat_hook_print` but the callback receives an
	:type:`hexchat_event_attrs *` with attributes related to the print event.

	:param ph: Plugin handle (as given to :func:`hexchat_plugin_init`).
	:param name: Name of the print event (as in *Text Events* window).
	:param pri: Priority of this command. Use :data:`HEXCHAT_PRI_NORM`.
	:param callb: Callback function. This will be called when this event name is printed.
	:param userdata: Pointer passed to the callback function.

	:returns: Pointer to the hook. Can be passed to :func:`hexchat_unhook`.

	.. versionadded:: 2.9.6


.. function:: hexchat_hook* hexchat_hook_server (hexchat_plugin *ph, const char *name, int pri, \
												int (*callb) (char *word[], char *word_eol[], void *user_data), void *userdata)

	Registers a function to be called when a certain server
	event occurs. You can use this to trap *PRIVMSG*, *NOTICE*, *PART*, a
	server numeric, etc. If you want to hook every line that comes from the
	IRC server, you may use the special name of *RAW LINE*.


	:param ph: Plugin handle (as given to :func:`hexchat_plugin_init`).
	:param name: Name of the server event.
	:param pri: Priority of this command. Use :data:`HEXCHAT_PRI_NORM`.
	:param callb: Callback function. This will be called when this event is received from the server.
	:param userdata: Pointer passed to the callback function.

	:returns: Pointer to the hook. Can be passed to :func:`hexchat_unhook`.

	**Example:**

	.. code-block:: c

		static int
		kick_cb (char *word[], char *word_eol[], void *userdata)
		{
			hexchat_printf (ph, "%s was kicked from %s (reason=%s)\n", word[4], word[3], word_eol[5]);
			return HEXCHAT_EAT_NONE;        /* don't eat this event, let other plugins and HexChat see it too */
		}

		hexchat_hook_server (ph, "KICK", HEXCHAT_PRI_NORM, kick_cb, NULL);


.. function:: hexchat_hook* hexchat_hook_server_attrs (hexchat_plugin *ph, const char *name, int pri, \
												int (*callb) (char *word[], char *word_eol[], hexchat_event_attrs *attrs, void *user_data), void *userdata)

	Registers a function to be called when a certain server
	event occurs. This is the same as
	:func:`hexchat_hook_server` but the callback receives an
	:type:`hexchat_event_attrs *` with attributes related to the server event.


	:param ph: Plugin handle (as given to :func:`hexchat_plugin_init`).
	:param name: Name of the server event.
	:param pri: Priority of this command. Use :data:`HEXCHAT_PRI_NORM`.
	:param callb: Callback function. This will be called when this event is received from the server.
	:param userdata: Pointer passed to the callback function.

	:returns: Pointer to the hook. Can be passed to :func:`hexchat_unhook`.

	.. versionadded:: 2.9.6

.. function:: hexchat_hook *hexchat_hook_timer (hexchat_plugin *ph, int timeout, \
												int (*callb) (void *user_data), void *userdata)

	Registers a function to be called every "timeout" milliseconds.

	:param ph: Plugin handle (as given to :func:`hexchat_plugin_init`).
	:param timeout: Timeout in milliseconds (1000 is 1 second).
	:param callb: Callback function. This will be called every "timeout" milliseconds.
	:param userdata: Pointer passed to the callback function.

	:returns: Pointer to the hook. Can be passed to :func:`hexchat_unhook`.

	**Example:**

	.. code-block:: c

		 static hexchat_hook *myhook;

		 static int
		 stop_cb (char *word[], char *word_eol[], void *userdata)
		 {
			 if (myhook != NULL)
			 {
					 hexchat_unhook (ph, myhook);
					 myhook = NULL;
					 hexchat_print (ph, "Timeout removed!\n");
			 }

			 return HEXCHAT_EAT_ALL;
		 }

		 static int
		 timeout_cb (void *userdata)
		 {
			 hexchat_print (ph, "Annoying message every 5 seconds! Type /STOP to stop it.\n");
			 return 1;       /* return 1 to keep the timeout going */
		 }

		 myhook = hexchat_hook_timer (ph, 5000, timeout_cb, NULL);
		 hexchat_hook_command (ph, "STOP", HEXCHAT_PRI_NORM, stop_cb, NULL, NULL);

.. function:: void* hexchat_unhook (hexchat_plugin *ph, hexchat_hook *hook)

	Unhooks any hook registered with **hexchat\_hook\_print/server/timer/command**. When plugins are unloaded,
	all of its hooks are automatically removed, so you don't need to call
	this within your :func:`hexchat_plugin_deinit` function.

	:param ph: Plugin handle (as given to :func:`hexchat_plugin_init`).
	:param hook: Pointer to the hook, as returned by **hexchat\_hook\_\***.

	:returns: The userdata you originally gave to **hexchat\_hook\_\***.


Context Functions
'''''''''''''''''

.. function:: hexchat_context* hexchat_find_context(hexchat_plugin *ph, const char *servname, const char *channel)

	Finds a context based on a channel and servername. If
	*servname* is NULL, it finds any channel (or query) by the given name.
	If *channel* is NULL, it finds the front-most tab/window of the given
	*servname*. If NULL is given for both arguments, the currently focused
	tab/window will be returned.

	:param ph: Plugin handle (as given to :func:`hexchat_plugin_init`).
	:param servname: Server name or NULL.
	:param channel: Channel name or NULL.

	:returns: Context pointer (for use with :func:`hexchat_set_context`) or NULL.


.. function:: hexchat_context* hexchat_get_context (hexchat_plugin *ph)

	Returns the current context for your plugin. You can
	use this later with :func:`hexchat_set_context`.

	:param ph: Plugin handle (as given to :func:`hexchat_plugin_init`).

	:returns: Context pointer (for use with :func:`hexchat_set_context`).


.. function:: int hexchat_set_context (hexchat_plugin *ph, hexchat_context *ctx)

	Changes your current context to the one given.

	:param ph: Plugin handle (as given to :func:`hexchat_plugin_init`).
	:param ctx: Context to change to (obtained with :func:`hexchat_get_context` or :func:`hexchat_find_context`).

	:returns:
		-  1: Success.
		-  0: Failure.


Plugin Preferences
''''''''''''''''''

.. function:: int hexchat_pluginpref_set_str (hexchat_plugin *ph, const char *var, const char *value)

	Saves a plugin-specific setting with string value to a plugin-specific config file.

	:param ph: Plugin handle (as given to :func:`hexchat_plugin_init`).
	:param var: Name of the setting to save.
	:param value: String value of the the setting.

	:returns:
		-  1: Success.
		-  0: Failure.

	**Example:**

	.. code-block:: c

		int
		hexchat_plugin_init (hexchat_plugin *plugin_handle, char **plugin_name, char **plugin_desc, char **plugin_version, char *arg)
		{
			ph = plugin_handle;
			*plugin_name = "Tester Thingie";
			*plugin_desc = "Testing stuff";
			*plugin_version = "1.0";

			hexchat_pluginpref_set_str (ph, "myvar1", "I want to save this string!");
			hexchat_pluginpref_set_str (ph, "myvar2", "This is important, too.");

			return 1;       /* return 1 for success */
		}

	In the example above, the settings will be saved to the
	plugin\_tester\_thingie.conf file, and its content will be: >myvar1 = I
	want to save this string!
	myvar2 = This is important, too.

	You should never need to edit this file manually.


.. function:: int hexchat_pluginpref_get_str (hexchat_plugin *ph, const char *var, char *dest)

	Loads a plugin-specific setting with string value from a plugin-specific config file.

	:param ph: Plugin handle (as given to :func:`hexchat_plugin_init`).
	:param var: Name of the setting to load.
	:param dest: Array to save the loaded setting's string value to.

	:returns:
		-  1: Success.
		-  0: Failure.


.. function:: int hexchat_pluginpref_set_int (hexchat_plugin *ph, const char *var, int value)

	Saves a plugin-specific setting with decimal value to a plugin-specific config file.

	:param ph: Plugin handle (as given to :func:`hexchat_plugin_init`).
	:param var: Name of the setting to save.
	:param value: Decimal value of the the setting.

	:returns:
		-  1: Success.
		-  0: Failure.

	**Example:**

	.. code-block:: c

		 static int
		 saveint_cb (char *word[], char *word_eol[], void *user_data)
		 {
			 int buffer = atoi (word[2]);

			 if (buffer > 0 && buffer &lt; INT_MAX)
			 {
					 if (hexchat_pluginpref_set_int (ph, "myint1", buffer))
					 {
							 hexchat_printf (ph, "Setting successfully saved!\n");
					 }
					 else
					 {
							 hexchat_printf (ph, "Error while saving!\n");
					 }
			 }
			 else
			 {
					 hexchat_printf (ph, "Invalid input!\n");
			 }

			 return HEXCHAT_EAT_HEXCHAT;
		 }

	You only need such complex checks if you're saving user input, which can
	be non-numeric.


.. function:: int hexchat_pluginpref_get_int (hexchat_plugin *ph, const char *var)

	Loads a plugin-specific setting with decimal value from a plugin-specific config file.

	:param ph: Plugin handle (as given to :func:`hexchat_plugin_init`).
	:param var: Name of the setting to load.

	:returns: The decimal value of the requested setting upon success, -1 for failure.


.. function:: int hexchat_pluginpref_delete (hexchat_plugin *ph, const char *var)

	Deletes a plugin-specific setting from a plugin-specific config file.

	:param ph: Plugin handle (as given to :func:`hexchat_plugin_init`).
	:param var: Name of the setting to delete.

	:returns:
		-  1: Success.
		-  0: Failure.

	If the given setting didn't exist, it also returns 1, so 1 only
	indicates that the setting won't exist after the call.


.. function:: int hexchat_pluginpref_list (hexchat_plugin *ph, char *dest)

	Builds a comma-separated list of the currently saved
	settings from a plugin-specific config file.

	:param ph: Plugin handle (as given to :func:`hexchat_plugin_init`).
	:param dest: Array of size 4096 to save the list to.

	:returns:
		-  1: Success.
		-  0: Failure (nonexistent, empty or inaccessible config file).

	**Example:**

	.. code-block:: c

		 static void
		 list_settings ()
		 {
			 char list[4096];
			 char buffer[512];
			 char *token;

			 hexchat_pluginpref_list (ph, list);
			 hexchat_printf (ph, "Current Settings:\n");
			 token = strtok (list, ",");

			 while (token != NULL)
			 {
					 hexchat_pluginpref_get_str (ph, token, buffer);
					 hexchat_printf (ph, "%s: %s\n", token, buffer);
					 token = strtok (NULL, ",");
			 }
		 }

	In the example above we query the list of currently stored settings,
	then print them one by one with their respective values. We always use
	*hexchat\_pluginpref\_get\_str ()*, and that's because we can read an
	integer as string (but not vice versa).

Plugin GUI
''''''''''

.. function:: void* hexchat_plugingui_add (hexchat_plugin *ph, const char *filename, const char *name, \
					const char *desc, const char *version, char *reserved)

	Adds a fake plugin to the GUI in :menuselection:`Window --> Plugins and Scripts`.
	This does not need to be done for your actual plugin and is only used for interfaces
	to other languages like our python plugin.

	:returns: Handle to be used with :func:`hexchat_plugingui_remove`

.. function:: void hexchat_plugingui_remove (hexchat_plugin *ph, void *handle)

	Removes the fake plugin from the GUI. Again not to be used to remove your own plugin.

	:param handle: Handle returned by :func:`hexchat_plugingui_add`

