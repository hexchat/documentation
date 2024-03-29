HexChat D-Bus Interface
=======================

For more help you can see the HexChat `plugin interface
documentation <plugins.html>`_ and see some examples in `Python <https://github.com/hexchat/hexchat/blob/master/src/common/dbus/example.py>`_ and `C <https://github.com/hexchat/hexchat/blob/master/src/common/dbus/example.c>`_.

.. WARNING:: The dbus interface may change in the future.

You can use the "/org/hexchat/Remote" object with interface
"org.hexchat.plugin", but his context can be changed by other clients at
any moment and you may receive signal asked by other clients. So for
more complex usage it's better to get your own remote object. Using
"Connect" method on interface "org.hexchat.connection"

Available methods on *org.hexchat.connection* interface:
--------------------------------------------------------

- "Connect"

  - Parameters:

    - gchar\*: filename
    - gchar\*: name
    - gchar\*: description
    - gchar\*: version

  - Returns:

    - gchar\*: Your own object's path.

- "Disconnect"

  - No parameter, no return value. It frees your remote object.

Available methods on *org.hexchat.plugin* interface:
----------------------------------------------------

- "Command"

  - Parameters:

    - gchar\*: the command name without the "/". (e.g. "nick pseudo")

- "Print"

  - Parameters:

    - gchar\*: text to print on the HexChat window.

- "FindContext"

  - Parameters:

    - gchar\*: the server name. Can be NULL.
    - gchar\*: the channel name. Can be NULL.

  - Returns:

    - guint: context ID.

- "GetContext"

  - Returns:

    - guint: current context's ID.

- "SetContext"

  - Parameters:

    - guint: context ID to switch, returned by "FindContext" or
      "GetContext"

  - Returns:

    - gboolean:

      - 1: Success.
      - 0: Failure.

- "GetInfo"

  - Parameters:

    - gchar\*: ID of the information you want.

  - Returns:

    - gchar\*: information you requested.

- "GetPrefs"

  - Parameters:

    - gchar\*: Setting name required.

  - Returns:

    - int:

      - 0: Failed.
      - 1: Returned a string.
      - 2: Returned an integer.
      - 3: Returned a boolean.

    - gchar\*: the information requested if it's a string.
    - int: the information requested if it's a integer or boolean.

- "HookCommand"

  - Parameters:

    - gchar\*: Name of the command (without the forward slash).
    - int: Priority of this command.
    - gchar\*: String of text to display when the user executes /help
      for this command. May be NULL if you're lazy.
    - int: Value to returns when the command is catched. See
      HEXCHAT\_EAT\_\*.

  - Returns:

    - guint: The ID of the hook.

- "HookServer"

  - Parameters:

    - gchar\*: Name of the server event.
    - int: Priority of this command.
    - int: Value to returns when the command is catched. See
      HEXCHAT\_EAT\_\*.

  - Returns:

    - guint: The ID of the hook.

- "HookPrint"

  - Parameters:

    - gchar\*: Name of the print event.
    - int: Priority of this command.
    - int: Value to returns when the command is catched. See
      HEXCHAT\_EAT\_\*.

  - Returns:

    - guint: The ID of the hook.

- "Unhook"

  - Parameters:

    - guint: ID of the hook to unhook. (the return value of
      "HookCommand", "HookServer" or "HookPrint")

- "ListGet"

  - Parameters:

    - gchar\*: The list name.

  - Returns:

    - guint: List ID.

- "ListNext"

  - Parameters:

    - guint: List ID returned by "ListGet".

  - Returns:

    - gboolean: says if there is no more item in the list.

- "ListStr"

  - Parameters:

    - guint: List ID returned by "ListGet".
    - gchar\*: Name of the information needed.

  - Returns:

    - gchar\*: The information requested.

Warning: "context" attribute of "channels" list should be get with
"ListInt"

- "ListInt"

  - Parameters:

    - guint: List ID returned by "ListGet".
    - gchar\*: Name of the information needed.

  - Returns:

    - guint: The information requested.

- "ListTime"

  - Parameters:

    - guint: List ID returned by "ListGet".
    - gchar\*: Name of the information needed.

  - Returns:

    - guint64: The information requested.

- "ListFields"

  - Parameters:

    - gchar\*: The list name.

  - Returns:

    - gchar\*\*: information names in this list.

- "ListFree"

  - Parameters:

    - guint: List ID returned by "ListGet".

- "EmitPrint"

  - Parameters:

    - gchar\*: Text event to print.
    - gchar\*\*: NULL terminated array of string.

  - Returns:

    - gboolean:

      - 1: Success.
      - 0: Failure.

- "Nickcmp"

  - Parameters:

    - gchar\*: String to compare.
    - gchar\*: String to compare.

  - Returns:

    - int: An integer less than, equal to, or greater than zero if s1
      is found, respectively, to be less than, to match, or be
      greater than s2.

- "Strip"

  - Parameters:

    - gchar\*: String to strip.
    - int: Length of the string (or -1 for NULL terminated).
    - int: Bit-field of flags:

      - 0: Strip mIRC colors.
      - 1: Strip text attributes.

  - Returns:

    - gchar\*: stripped string.

- "SendModes"

  - Parameters:

    - gchar\*\*: NULL terminated array of targets (strings). The
      names of people whom the action will be performed on.
    - int: Maximum modes to send per line.
    - gchar: Mode sign, '-' or '+'.
    - gchar: Mode char, e.g. 'o' for Ops.

Available signals:
------------------

- "ServerSignal"

  - Parameters:

    - gchar\*\*: word returned by HexChat.
    - gchar\*\*: word\_eol returned by HexChat.
    - guint: the ID of the hook (the return value of "HookServer").
    - guint: the ID of the context where the event come from.

- "CommandSignal"

  - Parameters:

    - gchar\*\*: word returned by HexChat.
    - gchar\*\*: word\_eol returned by HexChat.
    - guint: the ID of the hook (the return value of "HookCommand").
    - guint: the ID of the context where the event come from.

- "PrintSignal"

  - Parameters:

    - gchar\*\*: word returned by HexChat.
    - guint: the ID of the hook (the return value of "HookPrint").
    - guint: the ID of the context where the event come from.

- "UnloadSignal"

  - Emitted when the user asks to unload your program. Please exit(0);
    when received!
