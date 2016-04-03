HexChat Python Interface
========================

.. default-domain:: py

Features
--------

Here are some of the features of the python plugin interface:

- Comprehensive, consistent and straightforward API
- Load, unload, reload, and autoload support
- Per plugin independent interpreter state
- Python interactive console
- Python interactive command execution
- Python 2 and 3 support (2.9.6+)
- Full thread support (except for Python2 on Windows)
- Stdout and stderr redirected to HexChat console
- Dynamic list management
- Nice context treatment
- Plugin preferences


Python 2 or Python 3
~~~~~~~~~~~~~~~~~~~~

As of HexChat 2.9.6 the plugin supports both so which should you pick:

As a user most older scripts will not be updated for Python 3 so 2 is your best bet.

As a developer I would just recommend you make your scripts compatible for both but do note that
the Python 2 plugin on Windows does not support threads while the Python 3 plugin does.

Commands
--------

The Python plugin comes with a :command:`py` command that takes these arguments.

.. program:: py

.. option:: load <file>

    Load a script with given filename.
    :command:`/load` will also work.

.. option:: unload <filename\|module name>

    Unload module with given filename, or module name.
    :command:`/unload` will also work.

.. option:: reload <filename\|module name>

    Reload module with given filename, or module name.
    :command:`/reload` will also work.

.. option:: list

    List Python scripts loaded

.. option:: exec <command>

    Execute given Python command interactively. For example::

        /py exec import hexchat; print(hexchat.get_info('channel'))

.. option:: console

    Open the Python interactive console in a query
    ``>>python<<``. Every message sent will be intercepted by the Python
    plugin interface, and interpreted interactively. Notice that the
    console and /py exec commands live in the same interpreter state.

.. option:: about

    Show some information about the Python plugin interface.

Autoloading modules
-------------------

If you want some module to be autoloaded together with the Python plugin
interface (which usually loads at startup time), just make sure it has a
``.py`` extension and put it in the :file:`addons` subdir
of HexChat's `config directory <settings.html#config-files>`_.

Context theory
--------------

Before starting to explain what the API offers, I'll do a short
introduction about the HexChat context concept. Not because it's
something hard to understand, but because you'll understand better the
API explanations if you know what I'm talking about.

You can think about a context as an HexChat channel, server, or query
tab. Each of these tabs, has its own context, and is related to a given
server and channel (queries are a special kind of channel).

The *current* context is the one where HexChat passes control to the
module. For example, when HexChat receives a command in a specific
channel, and you have asked HexChat to tell you about this event, the
current context will be set to this channel before your module is
called.

Text Formatting
---------------

- Bold: ``'\002'``
- Color: ``'\003'``
- Hidden: ``'\010'``
- Underline: ``'\037'``
- Original Attributes: ``'\017'``
- Reverse Color: ``'\026'``
- Beep: ``'\007'``
- Italics: ``'\035'`` (2.10.0+)

For example this will print underlined red text:

.. code-block:: python

    print('\037\00304Text!')


Bit fields
----------

Some lists return bit fields which many Python scripters may not be familair with
so here is an example of how to use one:

.. code-block:: python

    ...

    # We know its the 15th bit field we want, this will get that value
    text_strip = 1 << 15
    text_strip_unset = 1 << 16 # If this is set use the global option

    def get_chanopt (channel, option):
        for chan in hexchat.get_list('channels'):
            if chan.channel == channel:
                return bool(chan.flags & option)

    if get_chanopt('#hexchat', text_strip_unset):
        stripped = bool(hexchat.get_prefs('text_stripcolor_msg'))
    else:
        stripped = get_chanopt ('#hexchat', text_strip)

    print('Color stripping in #hexchat is: {}'.format(stripped))


Hello world
-----------

Here is the traditional *hello world* example.

.. code-block:: python

    __module_name__ = "helloworld"
    __module_version__ = "1.0"
    __module_description__ = "Python module example"

    print("Hello world!")

This module will print "Hello world!" in the HexChat console, and sleep
forever until it's unloaded. It's a simple module, but already
introduces some concepts. Notice how the module information is set. This
information is obligatory, and will be shown when listing the loaded
HexChat modules.

.. module:: hexchat

hexchat module
--------------

The hexchat module is your passport to every HexChat functionality offered
by the Python plugin interface. Here's a simple example:

.. code-block:: python

    import hexchat
    hexchat.prnt("Hi everyone!")

The following functions are available in the hexchat module.

Constants and Attributes
~~~~~~~~~~~~~~~~~~~~~~~~

.. data:: PRI_HIGHEST
          PRI_HIGH
          PRI_NORM
          PRI_LOW
          PRI_LOWEST

    Priority given to hooks.

.. data:: EAT_PLUGIN
          EAT_HEXCHAT
          EAT_ALL
          EAT_NONE

    Used as return values for callbacks.

.. attribute:: __version__

    Tuple of (MAJOR_VERSION, MINOR_VERSION)

Generic functions
~~~~~~~~~~~~~~~~~

.. function:: prnt(string)

    This function will print string in the current context. It's mainly
    useful as a parameter to pass to some other function, since the usual
    print statement will have the same results. You have a usage example
    above.

    This function is badly named because ``"print"`` is a reserved keyword
    of the Python language until Python 3.

.. function:: emit_print(event_name, *args)

    This function will generate a *print event* with the given arguments. To
    check which events are available, and the number and meaning of
    arguments, have a look at the :menuselection:`Settings --> Text Events` window.
    Here is one example:

    .. code-block:: python

        hexchat.emit_print("Channel Message", "John", "Hi there", "@")

    With plugin version 1.0+ this function takes keywords for certain attributes such as *time*.

.. function:: command(string)

    Execute the given command in the current *context*. This has the same
    results as executing a command in the HexChat window, but notice that
    the ``/`` prefix is not used. Here is an example:

    .. code-block:: python

        hexchat.command("server irc.openprojects.net")

    A list of commands is provided here: :ref:`commands_list`.

.. function:: nickcmp(s1, s2)

    This function will do an RFC1459 compliant string comparison
    and is useful to compare channels and nicknames.

    :returns: Returns 0 if they match and less than or greater than 0 if s1 is less than or greather than s2

    .. code-block:: python

        if hexchat.nickcmp(nick, "mynick") == 0:
            print("They are the same!")

.. function:: strip(text[, length=-1, flags=3])

    This function can strip colors and attributes from text.

    :param length: -1 for entire string
    :param flags:
        1: Strip Colors
        2: Strip Attributes
        3: Strip All
    :returns: Stripped String

    .. code-block:: python

        text = '\00304\002test' # Bold red text
        print(text)
        print(hexchat.strip(text, len(text), 1)) # Bold uncolored text

Information retrieving functions
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. function:: get_info(type)

    Retrieve the information specified by the ``type`` string in the current
    context. At the moment of this writing, the following information types
    are available to be queried:

    - **away:** Away reason or None if you are not away.
    - **channel:** Channel name of the current context.
    - **charset:** Charset in current context.
    - **configdir:** HexChat config directory e.g.: "~/.config/hexchat".
    - **event\_text NAME:** Returns text event string for requested event.
    - **gtkwin\_ptr:** Returns hex representation of the pointer to the current Gtk window.
    - **host:** Real hostname of the server you connected to.
    - **inputbox:** Contents of inputbox.
    - **network:** Current network name or None.
    - **nick:** Your current nick name.
    - **nickserv:** Current networks nickserv password or None.
    - **modes:** Current channel modes or None.
    - **server:** Current server name (what the server claims to be) or
      None if you are not connected.
    - **topic:** Current channel topic.
    - **version:** HexChat version number.
    - **win\_status:** Returns status of window: 'active', 'hidden', or
      'normal'.

    Example:

    .. code-block:: python

        if hexchat.get_info("network") == 'freenode':
            hexchat.prnt('connected!')

    You can also get the format of Text Events by using *event_text* and the event:

    .. code-block:: python

        print(hexchat.get_info("event_text Channel Message"))

.. function:: get_prefs(name)

    Retrieve the HexChat setting information specified by the ``name``
    string, as available by the ``/set`` command.

    .. code-block:: python

        print("Current preferred nick: " + hexchat.get_prefs("irc_nick1"))

    A list of settings is provided here: :ref:`settings_list`.

    On top of that there are a few special preferences:

    - **id**: unique server id
    - **state_cursor**: location of cursor in input box


.. function:: get_list(type)

    With this function you may retrieve a list containing the selected
    information from the current context, like a DCC list, a channel list, a
    user list, etc. Each list item will have its attributes set dynamically
    depending on the information provided by the list type.

    The example below is a rewrite of the example provided with HexChat's
    plugin API documentation. It prints a list of every DCC transfer
    happening at the moment. Notice how similar the interface is to the C
    API provided by HexChat.

    .. code-block:: python

        list = hexchat.get_list("dcc")
        if list:
            print("--- DCC LIST ------------------")
            print("File  To/From   KB/s   Position")
            for i in list:
                print("%6s %10s %.2f  %d" % (i.file, i.nick, i.cps/1024, i.pos))

    Below you will find what each list type has to offer.

List Types
''''''''''

channels
^^^^^^^^

The channels list type gives you access to the channels, queries and
their servers. The following attributes are available in each list item:

- **channel:** Channel or query name.
- **channelkey:** Channel key. (2.9.6+)
- **chantypes:** Channel types e.g. #!&.
- **context:** A context object, giving access to that channel/server.
- **id:** Unique server id.
- **lag:** Latency in milliseconds.
- **maxmodes:** Max modes per line.
- **network:** Network name to which this channel belongs.
- **nickprefixes:** Nickname prefixes e.g. @%+.
- **nickmodes:** Nickname mode chars e.g. ov.
- **queue:** Number of bytes in the send-queue.
- **server:** Server name to which this channel belongs.
- **users:** Number of users in the channel.
- **type:** Type of context.

  - 1: Server
  - 2: Channel
  - 3: Dialog
  - 4: Notices
  - 5: SNotices

- **flags:** Bit field of flags:

  - 0: Connected
  - 1: Connecting
  - 2: Away
  - 3: End of MOTD (Login Complete)
  - 4: Has WHOX
  - 5: Has IDMSG
  - 6: Join/Parts hidden
  - 7: Join/Parts hidden unset
  - 8: Beep on Message
  - 9: Blink Tray
  - 10: Blink Task Bar
  - 11: Logging (This and the following are 2.10.0+)
  - 12: Logging unset
  - 13: Scrollback
  - 14: Scrollback unset
  - 15: Strip Colors
  - 16: Strip Colors unset

dcc
^^^

The dcc list type gives you access to a list of DCC file transfers. The
following attributes are available in each list item:

- **address32:** Address of the remote user (ipv4 address, as an int).
- **cps:** Bytes per second (speed).
- **destfile:** Destination full pathname.
- **file:** Filename.
- **nick:** Nickname of person who the file is from/to.
- **port:** TCP port number.
- **pos:** Bytes sent/received.
- **resume:** Point at which this file was resumed (or zero if it was
  not resumed).
- **size:** File size in bytes.
- **status:** DCC status:

  - 0: queued
  - 1: active
  - 2: failed
  - 3: done
  - 4: connecting
  - 5: aborted

- **type:** DCC type:

  - 0: send
  - 1: receive
  - 2: chatrecv
  - 3: chatsend

users
^^^^^

The users list type gives you access to a list of users in the current
channel. The following attributes are available in each list item:

- **account:** Account name or None (2.9.6+)
- **away:** Away status.
- **host:** Host name in the form user\@host (or None, if not known).
- **lasttalk:** Time they last talked (2.9.6+)
- **nick:** Nick name.
- **prefix:** Prefix character, .e.g: @ or +. Points to a single char.
- **realname:** Real name.
- **selected:** Selected status in the userlist.

ignore
^^^^^^

The ignore list type gives you access to the current ignored list. The
following attributes are available in each list item:

- **mask:** Ignore mask (for example, "\*\!\*\@\*.aol.com").
- **flags:** Bit field of flags:

  - 0: private
  - 1: notice
  - 2: channel
  - 3: ctcp
  - 4: invite
  - 5: unignore
  - 6: nosave
  - 7: dcc

notify
^^^^^^

The notify list shows users on your friends list and their status:

- **nick:** Users nickname
- **networks:** Networks they are setup to notify on (None for all)
- **flags:** 0 is offline, 1 is online
- **on:** Time when user last came on (2.9.6+)
- **off:** Time when user last logged off (2.9.6+)
- **seen:** Time when user was last seen (2.9.6+)

Hook functions
~~~~~~~~~~~~~~

These functions allow one to hook into HexChat events.

Parameters
''''''''''

callback
^^^^^^^^

A callback is the function that will be called when the event happens.

The callback supposed to return one of the EAT\_\* `constants <script_python.html#constants-and-attributes>`_,
it is able control how HexChat will proceed after the callback returns. These
are the available constants, and their meanings:

- :data:`EAT_PLUGIN`: Don't let any other plugin receive this event.
- :data:`EAT_HEXCHAT`: Don't let HexChat treat this event as usual.
- :data:`EAT_ALL`: Eat the event completely.
- :data:`EAT_NONE`: Let everything happen as usual.

.. Note:: Returning ``None`` is the same as returning :data:`EAT\_NONE`.

userdata
^^^^^^^^

The parameter userdata, if given, allows you to pass a custom object to
your callback.

attributes
^^^^^^^^^^

If you create a hook with :func:`hook_server_attrs` or :func:`hook_print_attrs` the last
argument in the callback will be an `Attribute` object.

.. object:: Attribute

    .. attribute:: Attribute.time

        The time the event occurred (from server-time) or 0

priority
^^^^^^^^

When a priority keyword parameter is accepted, it means that this
callback may be hooked with five different priorities which are
`constants <script_python.html#constants-and-attributes>`_ will define the
order in which your plugin will be called. Most of the time, you won't
want to change its default value (:data:`PRI_NORM`).

word and word\_eol
^^^^^^^^^^^^^^^^^^

These parameters, when available in a command or server callback, are lists of strings
which contain the parameters the user entered for the particular
command. For example, if you executed::

    /command NICK Hi there!

- **word[0]** is ``command``
- **word[1]** is ``NICK``
- **word[2]** is ``Hi``
- **word[3]** is ``there!``
- **word\_eol[0]** is ``command NICK Hi there!``
- **word\_eol[1]** is ``NICK Hi there!``
- **word\_eol[2]** is ``Hi there!``
- **word\_eol[3]** is ``there!``

These parameters are also used in print events. When created by these events
they have a completely different meaning though. Text events (:menuselection:`Settings --> Text events`)
have numbered arguments associated with them, these apply to the item in the **word** list.
For example on a "Channel Message" event:

    [23:29:26] <@Nick> hello everyone

- **word[0]** is ``Nick``
- **word[1]** is ``hello everyone``
- **word[2]** is ``@``
- **word_eol[0]** is ``Nick hello everyone @``
- **word_eol[1]** is ``hello everyone @``
- **word_eol[2]** is ``@``

.. function:: hook_command(name, callback[, userdata=None, priority=PRI_NORM, help=None])

    This function allows you to hook into the name HexChat command. It means
    that everytime you type ``/name ...``, ``callback`` will be called.
    Parameters ``userdata`` and ``priority`` have their meanings explained
    above, and the parameter help, if given, allows you to pass a help text
    which will be shown when ``/help name`` is executed. If the command starts
    with a period it will not show up in ``/help`` though.

    You may also hook an empty string to capture every message a user sends,
    either when they hit enter or use ``/say``.

    :returns: New Hook Handler

    .. code-block:: python

        def onotice_cb(word, word_eol, userdata):
            if len(word) < 2:
                print("Second arg must be the message!")
            else:
                hexchat.command("NOTICE @{} {}".format(hexchat.get_info("channel"), word_eol[1]))
            return hexchat.EAT_ALL

        hexchat.hook_command("ONOTICE", onotice_cb, help="/ONOTICE <message> Sends a notice to all ops")

    You may return one of ``EAT_*`` constants in the callback, to control
    HexChat's behavior, as explained above.

.. function:: hook_print(name, callback[, userdata=None, priority=PRI_NORM])

    This function allows you to register a callback to trap any print
    events. The event names are available in the :menuselection:`Settings --> Text Events` window.
    Parameters ``userdata`` and ``priority`` have their meanings explained
    above.

    :param name: event name (see :menuselection:`Settings --> Text Events`)
    :returns: New Hook Handler

    .. code-block:: python

        def youpart_cb(word, word_eol, userdata):
            print("You have left channel " + word[2])
            return hexchat.EAT_HEXCHAT # Don't let HexChat do its normal printing

        hexchat.hook_print("You Part", youpart_cb)

    Along with Text Events there are a handfull of *special* events you can hook with this:

    - **Open Context**: Called when a new context is created.
    - **Close Context**: Called when a context is closed.
    - **Focus Tab**: Called when a tab is brought to front.
    - **Focus Window**: Called a toplevel window is focused, or the main tab-window is focused by the window manager.
    - **DCC Chat Text**: Called when some text from a DCC Chat arrives. It provides these elements in the word list:

      - Address
      - Port
      - Nick
      - Message

    - **Key Press**: Called when some keys are pressed in the input box. It provides these elements in the word list:

      - Key Value
      - State Bitfield (shift, capslock, alt)
      - String version of the key
      - Length of the string (may be 0 for unprintable keys)

.. function:: hook_print_attrs(name, callback[, userdata=None, priority=PRI_NORM])

    This function is the same as :func:`hook_print` except its callback will have a new
    `Attribute` argument.

    :returns: New Hook Handler

    .. versionadded:: 1.0

    .. code-block:: python

        def youpart_cb(word, word_eol, userdata, attributes):
            if attributes.time: # Time may be 0 if server-time is not enabled.
                print("You have left channel {} at {}".format(word[2], attributes.time))
                return hexchat.EAT_HEXCHAT

        hexchat.hook_print_attrs("You Part", youpart_cb)

.. function:: hook_server(name, callback[, userdata=None, priority=PRI_NORM])

    This function allows you to register a callback to be called when a
    certain server event occurs. You can use this to trap ``PRIVMSG``,
    ``NOTICE``, ``PART``, a server numeric, etc. Parameters ``userdata`` and
    ``priority`` have their meanings explained above.

    You can hook the special event "RAW LINE" to capture all server events.

    :returns: New Hook Handler

    .. code-block:: python

        def kick_cb(word, word_eol, userdata):
            print('{} was kicked from {} ({})'.format(word[3], word[2], word_eol[4]))
            # Don't eat this event, let other plugins and HexChat see it too
            return hexchat.EAT_NONE

       hexchat.hook_server("KICK", kick_cb)

.. function:: hook_server_attrs(name, callback[, userdata=None, priority=PRI_NORM])

    This function is the same as :func:`hook_server` Except its callback will have a new
    `Attribute` argument.

    :returns: New Hook Handler

    .. versionadded:: 1.0

    .. code-block:: python

        def kick_cb(word, word_eol, userdata, attributes):
            if attributes.time: # Time may be 0 if server-time is not enabled.
                print('He was kicked at {}'.format(attributes.time))
                return hexchat.EAT_NONE

        hexchat.hook_server_attrs("KICK", kick_cb)

.. function:: hook_timer(timeout, callback[, userdata=None])

    This function allows you to register a callback to be called every
    timeout milliseconds. Parameters userdata and priority have their
    meanings explained above.

    :returns: New Hook Handler

    .. code-block:: python

        myhook = None

        def stop_cb(word, word_eol, userdata):
            global myhook
            if myhook is not None:
                hexchat.unhook(myhook)
                myhook = None
                print("Timeout removed!")

        def timeout_cb(userdata):
            print("Annoying message every 5 seconds! Type /STOP to stop it.")
            return 1 # Keep the timeout going

        myhook = hexchat.hook_timer(5000, timeout_cb)
        hexchat.hook_command("STOP", stop_cb)

    If you return a true value from the callback, the timer will be kept,
    otherwise it is removed.

.. function:: hook_unload(callback[, userdata=None])

    This function allows you to register a callback to be called when the
    plugin is going to be unloaded. Parameters ``userdata`` and ``priority``
    have their meanings explained above.

    :returns: New Hook Handler

    .. code-block:: python

        def unload_cb(userdata):
            print("We're being unloaded!")

        hexchat.hook_unload(unload_cb)

.. function:: unhook(handler)

    Unhooks any hook registered with the hook functions above.

    :param handler: Handler returned from :func:`hook_print`, :func:`hook_command`, :func:`hook_server` or :func:`hook_timer`

    As of version 1.0 of the plugin hooks from :func:`hook_print` and :func:`hook_command` can be unhooked by their names.

Plugin preferences
~~~~~~~~~~~~~~~~~~

You can use pluginpref to easily store and retrieve settings.

.. function:: set_pluginpref(name, value)

    Stores settings in addon\_python.conf in the config dir.

    :returns:
        - False: Failure
        - True: Success

    .. versionadded:: 0.9

    .. note:: Until the plugin uses different a config file per script it's
              recommened to use 'scriptname_settingname' to avoid conflicts.

.. function:: get_pluginpref(name)

    This will return the value of the variable of that name. If there is
    none by this name it will return ``None``.

    :returns: String or Integer of stored setting or None if it does not exist.

    .. note:: Strings of numbers and booleans are always returned as Integers.

    .. versionadded:: 0.9

.. function:: del_pluginpref(name)

    Deletes the specified variable.

    :returns:
        - False: Failure
        - True: Success (or never existing),

    .. versionadded:: 0.9

.. function:: list_pluginpref()

    Returns a list of all currently set preferences.

    :rtype: List of Strings

    .. versionadded:: 0.9

Context handling
~~~~~~~~~~~~~~~~

Below you will find information about how to work with contexts.

Context objects
'''''''''''''''

As explained in the Context theory session above, contexts give access
to a specific channel/query/server tab of HexChat. Every function
available in the xchat module will be evaluated in the current context,
which will be specified by HexChat itself before passing control to the
module. Sometimes you may want to work in a specific context, and that's
where `context` objects come into play.

You may create a context object using :func:`get_context` or :func:`find_context`
functions as explained below, or trough the :func:`get_list` function, as explained above.


.. function:: get_context()

    :rtype: `context`

.. function:: find_context(server=None, channel=None)

    Finds a context based on a channel and servername or if no parameters are given returns
    the current (front) context.

    :keyword server: if None only looks for channel name
    :keyword channel: if None looks for front context of given server
    :rtype: `context`

    .. code-block:: python

        cnc = hexchat.find_context(channel='#conectiva')
        cnc.command('whois niemeyer')


.. object:: context

    The context object returned by the functions listed above has these methods:

    .. method:: context.set()

        Changes the current context to be the one represented by this context object.

    .. method:: context.prnt(string)

        Does the same as the :func:`prnt` function but in the given context.

    .. method:: context.emit_print(event_name, \*args)

        Does the same as the :func:`emit_print` function but in the given context.

    .. method:: context.command(string)

        Does the same as the :func:`command` function but in the given context

    .. method:: context.get_info(type)

        Does the same as the :func:`get_info` function but in the given context.

    .. method:: context.get_list(type)

        Does the same as the :func:`get_list` function but in the given context.

--------------

Maintained by: TingPing

Original Author: Gustavo Niemeyer
`gustavo@niemeyer.net <mailto:gustavo@niemeyer.net>`_
