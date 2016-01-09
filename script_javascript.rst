HexChat Javascript Interface
============================

.. default-domain:: js

Information
-----------

The javascript plugin does not come with HexChat, more information on it can be found `Here <https://github.com/TingPing/hexchat-javascript>`_

This page is simply for hosting its API docs and is a work in progress.

Functions
---------

Generic Fuctions
~~~~~~~~~~~~~~~~

.. function:: print(object)

    Prints text to the current context.

    :param object: Object to be converted to a string

.. function:: command(string)

    Runs a command in the current context.

Hook Functions
~~~~~~~~~~~~~~

.. function:: hook_print(name, callback[, userdata, priority])

    Calls specified callback anytime the specified event happens.
    The Print Events can be found in :menuselection:`Settings --> Text Events`.

    :returns: Hook handler

.. function:: hook_server(name, callback[, userdata, priority])

    Calls specified callback anytime the specified event happens.

    :param name: IRC numeric or named event
    :returns: Hook handler

.. function:: hook_command(name, callback[, userdata, priority])

    Calls specified callback anytime the specified command is run.

    :returns: Hook handler

.. function:: hook_special(name, callback[, userdata, priority])

    Calls specified callback anytime the specified special event happens.

    Special Events:

    - Open Context
    - Close Context
    - Focus Tab
    - Focus Window
    - DCC Chat Text
    - Key Press

    :returns: Hook handler

.. function:: hook_timer(timeout, callback[, userdata])

    Calls specified callback in your specified interval (in milliseconds).

    If you return *true* the callback will continue to be called, otherwise it is removed.

    :returns: Hook handler

.. function:: hook_unload(callback[, userdata])

    Calls specified callback when the script is unloaded.

    :returns: Hook handler

.. function:: unhook(handler)

    Removes any hook registered above.

Plugin Preferences
~~~~~~~~~~~~~~~~~~

You can use pluginpref to easily store and retrieve settings.

.. function:: set_pluginpref(name, value)

    Stores settings in addon\_*SCRIPT_NAME*.conf in the config dir.

    :returns:
        - False: Failure
        - True: Success

.. function:: get_pluginpref(name)

    This will return the value of the variable of that name. If there is
    none by this name it will return undefined.

    :returns: String or Integer of stored setting or None if it does not exist.

    .. Note:: Strings of numbers are always returned as Integers.

.. function:: del_pluginpref(name)

    Deletes the specified variable.

    :returns:
        - False: Failure
        - True: Success (or never existing),

.. function:: list_pluginpref()

    Returns a list of all currently set preferences.
