HexChat Perl Interface
======================

Introduction
------------

This is the Perl interface for HexChat. If there are any problems,
questions, comments or suggestions please email them to the address on
the bottom of this page.

Constants
---------

Priorities
~~~~~~~~~~

-  **Xchat::PRI_HIGHEST**
-  **Xchat::PRI_HIGH**
-  **Xchat::PRI_NORM**
-  **Xchat::PRI_LOW**
-  **Xchat::PRI_LOWEST**

Return values
~~~~~~~~~~~~~

-  **Xchat::EAT_NONE** - pass the event along
-  **Xchat::EAT_XCHAT** - don't let HexChat see this event
-  **Xchat::EAT_PLUGIN** - don't let other scripts and plugins see
   this event but xchat will still see it
-  **Xchat::EAT_ALL** - don't let anything else see this event

Timer and fd hooks
^^^^^^^^^^^^^^^^^^

-  **Xchat::KEEP** - keep the timer going or hook watching the
   handle
-  **Xchat::REMOVE** - remove the timer or hook watching the handle

hook\_fd flags
~~~~~~~~~~~~~~

-  **Xchat::FD_READ** - invoke the callback when the handle is ready
   for reading
-  **Xchat::FD_WRITE** - invoke the callback when the handle is
   ready for writing
-  **Xchat::FD_EXCEPTION** - invoke the callback if an exception
   occurs
-  **Xchat::FD_NOTSOCKET** - indicate that the handle being hooked
   is not a socket

Functions
---------

Xchat::register( $name, $version, [$description,[$callback]] )
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

-  ``$name`` - The name of this script
-  ``$version`` - This script's version
-  ``$description`` - A description for this script
-  ``$callback`` - This is a function that will be called when the is
   script unloaded. This can be either a reference to a function or an
   anonymous sub reference.

This is the first thing to call in every script.

Xchat::hook_server( $message, $callback, [\%options] )
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Xchat::hook_command( $command, $callback, [\%options] )
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Xchat::hook_print( $event,$callback, [\%options] )
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Xchat::hook_timer( $timeout,$callback, [\%options | $data] )
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Xchat::hook_fd( $handle, $callback, [ \%options ] )
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

These functions can be to intercept various events. hook\_server can be
used to intercept any incoming message from the IRC server.
hook\_command can be used to intercept any command, if the command
doesn't currently exist then a new one is created. hook\_print can be
used to intercept any of the events listed in *Setttings* ``->`` *Text
Events*. hook\_timer can be used to create a new timer

-  **$message** - server message to hook such as PRIVMSG
-  **$command** - command to intercept, without the leading /
-  **$event** - one of the events listed in *Settings* ``->`` *Text
   Events*
-  **$timeout** - timeout in milliseconds
-  **$handle** - the I/O handle you want to monitor with hook\_fd.
   This must be something that has a fileno. See perldoc -f fileno or
   `fileno <http://perldoc.perl.org/functions/fileno.html>`_
-  **$callback** - callback function, this is called whenever the
   hooked event is trigged, the following are the conditions that will
   trigger the different hooks. This can be either a reference to a
   function or an anonymous sub reference.
-  **\%options** - a hash reference containing addional options for
   the hooks

Valid keys for %options:

.. raw:: html

   <table border="1">   <tr>
      <td>

data

.. raw:: html

   </td>  <td>

Additional data that is to be associated with the hook. For timer hooks
this value can be provided either as Xchat::hook\_timer( $timeout,
:math:`cb,{data=&gt;`\ data}) or Xchat::hook\_timer( $timeout, $cb,
$data ). However, this means that hook\_timer cannot be provided with a
hash reference containing data as a key. example: my $options = { data
=> [@arrayOfStuff] }; Xchat::hook\_timer( $timeout, $cb, $options ); In
this example, the timer's data will be [@arrayOfStuff] and not { data =>
[@arrayOfStuff] } This key is valid for all of the hook functions.
Default is undef.

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

priority

.. raw:: html

   </td> <td>

Sets the priority for the hook. It can be set to one of the
Xchat::PRI\_\* constants. This key only applies to server, command and
print hooks. Default is Xchat::PRI\_NORM.

.. raw:: html

   </td>   </tr>   <tr>
         <td>

help\_text

.. raw:: html

   </td>   <td>

Text displayed for /help $command. This key only applies to command
hooks. Default is "".

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

flags

.. raw:: html

   </td>   <td>

Specify the flags for a fd hook. See hook fd flags section for valid
values. On Windows if the handle is a pipe you specify
Xchat::FD\_NOTSOCKET in addition to any other flags you might be using.
This key only applies to fd hooks. Default is Xchat::FD\_READ

.. raw:: html

   </td>
      </tr></table><p>

When callbacks are invoked
^^^^^^^^^^^^^^^^^^^^^^^^^^

Each of the hooks will be triggered at different times depending on the
type of hook.

.. raw:: html

   <table border="1">   <tr style="background-color: #dddddd">
         <td>

Hook Type

.. raw:: html

   </td>   <td>

When the callback will be invoked

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

server hooks

.. raw:: html

   </td>   <td>

a
\ :math:`message</code> message is received from the server </td> </tr> <tr> <td>command hooks</td> <td>the <code>`\ command
command is executed, either by the user or from a script

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

print hooks

.. raw:: html

   </td> <td>

X-Chat is about to print the message for the
\ :math:`event</code> event </td> </tr> <tr> <td>timer hooks</td> <td>called every <code>`\ timeout
milliseconds (1000 millisecond is 1 second) the callback will be
executed in the same context where the hook\_timer was called, if the
context no longer exists then it will execute in a random context

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

fd hooks

.. raw:: html

   </td> <td>

depends on the flags that were passed to hook\_fd See hook\_fd flags
section.

.. raw:: html

   </td>
      </tr>
   </table>

The value return from these hook functions can be passed to
``Xchat::unhook`` to remove the hook.

Callback Arguments
^^^^^^^^^^^^^^^^^^

All callback functions will receive their arguments in ``@_`` like every
other Perl subroutine.

Server and command callbacks

``$_[0]`` - array reference containing the IRC message or command and
arguments broken into words example:
/command arg1 arg2 arg3
``$_[0][0]`` - command
``$_[0][1]`` - arg1
``$_[0][2]`` - arg2
``$_[0][3]`` - arg3

``$_[1]`` - array reference containing the Nth word to the last word
example:
/command arg1 arg2 arg3
``$_[1][0]`` - command arg1 arg2 arg3
``$_[1][1]`` - arg1 arg2 arg3
``$_[1][2]`` - arg2 arg3
``$_[1][3]`` - arg3

``$_[2]`` - the data that was passed to the hook function

Print callbacks

``$_[0]`` - array reference containing the values for the text event,
see *Settings* ``->`` *Text Events*
``$_[1]`` - the data that was passed to the hook function

Timer callbacks

``$_[0]`` - the data that was passed to the hook function

fd callbacks

``$_[0]`` - the handle that was passed to hook\_fd ``$_[1]`` - flags
indicating why the callback was called ``$_[2]`` - the data that was
passed to the hook function

Callback return values
^^^^^^^^^^^^^^^^^^^^^^

All server, command and print callbacks should return one of the
Xchat::EAT_* constants.
Timer callbacks can return ``Xchat::REMOVE`` to remove the timer or
``Xchat::KEEP`` to keep it going.

Miscellaneous Hook Related Information
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

For server hooks, if ``$message`` is "RAW LINE" then ``$cb``> will be
called for every IRC message that HexChat receives.

For command hooks if ``$command`` is "" then ``$cb`` will be called for
messages entered by the user that is not a command.

For print hooks besides those events listed in *Settings* ``->`` *Text
Events*, these additional events can be used.

.. raw:: html

   <table border="1">   <tr style="background-color: #dddddd">
         <td>

Event

.. raw:: html

   </td> <td>

Description

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

"Open Context"

.. raw:: html

   </td> <td>

a new context is created

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

"Close Context"

.. raw:: html

   </td>   <td>

a context has been close

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

"Focus Tab"

.. raw:: html

   </td> <td>

when a tab is brought to the front

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

"Focus Window"

.. raw:: html

   </td> <td>

when a top level window is focused or the main tab window is focused by
the window manager

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

"DCC Chat Text"

.. raw:: html

   </td>   <td>

when text from a DCC Chat arrives.
\ :math:`_[0]</code> will have these values<br /> <br /> <code>`\ *[0][0]
- Address \ :math:`_[0][1]</code> - Port<br /> <code>`*\ [0][2] - Nick
$\_[0][3] - Message

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

"Key Press"

.. raw:: html

   </td> <td>

used for intercepting key presses $\ *[0][0] - key value $*\ [0][1] -
state bitfield, 1 - shift, 4 - control, 8 - alt $\ *[0][2] - string
version of the key which might be empty for unprintable keys $*\ [0][3]
- length of the string in $\_[0][2]

.. raw:: html

   </td>
      </tr>
   </table>

Xchat::unhook( $hook )
~~~~~~~~~~~~~~~~~~~~~~~~~~

-  **$hook** - the hook that was previously returned by one of the
   Xchat::hook_* functions

This function is used to removed a hook previously added with one of the
Xchat::hook_* functions.

It returns the data that was passed to the Xchat::hook_* function
when the hook was added.

Xchat::print( $text | \@lines, [$channel,[$server]] )
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

-  **$text** - the text to print
-  **\@lines** - array reference containing lines of text to be
   printed all the elements will be joined together before printing
-  **$channel** - channel or tab with the given name where ``$text``
   will be printed
-  **$server** - specifies that the text will be printed in a channel
   or tab that is associated with ``$server``

The first argument can either be a string or an array reference of
strings. Either or both of ``$channel`` and ``$server`` can be undef.

If called as Xchat::print( $text ), it will always return true. If
called with either the channel or the channel and the server specified
then it will return true if a context is found and false otherwise. The
text will not be printed if the context is not found. The meaning of
setting ``$channel`` or ``$server`` to undef is the same as
find\_context.

Xchat::printf( $format, LIST )
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

-  **$format** - a format string, see "perldoc -f
   `sprintf <http://perldoc.perl.org/functions/sprintf.html>`_" for
   further details
-  **LIST** - list of values for the format fields

Xchat::command( $command | \@commands, [$channel,[$server]] )
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

-  **$command** - the command to execute, without the leading /
-  **\@commands** - array reference containing a list of commands to
   execute
-  **$channel** - channel or tab with the given name where
   ``$command`` will be executed
-  **$server** - specifies that the command will be executed in a
   channel or tab that is associated with ``$server``

The first argument can either be a string or an array reference of
strings. Either or both of ``$channel`` and ``$server`` can be undef.

If called as Xchat::command( $command ), it will always return true.
If called with either the channel or the channel and the server
specified then it will return true if a context is found and false
otherwise. The command will not be executed if the context is not found.
The meaning of setting ``$channel`` or ``$server`` to undef is the same
as find\_context.

Xchat::commandf( $format, LIST )
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

-  **$format** - a format string, see "perldoc -f
   `sprintf <http://perldoc.perl.org/functions/sprintf.html>`_" for
   further details
-  **LIST** - list of values for the format fields

Xchat::find_context( [$channel, [$server]] )
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

-  **$channel** - name of a channel
-  **$server** - name of a server

Either or both of ``$channel`` and ``$server`` can be undef. Calling
Xchat::find_context() is the same as calling
Xchat::find_context( undef, undef) and
Xchat::find_context( $channel ) is the same as
Xchat::find_context( $channel, undef ).

If ``$server`` is undef, find any channel named ``$channel``. If
``$channel`` is undef, find the front most window or tab named
``$server``.If both ``$channel`` and ``$server`` are undef, find the
currently focused tab or window.

Return the context found for one of the above situations or undef if
such a context cannot be found.

Xchat::get_context()
~~~~~~~~~~~~~~~~~~~~~~~~

Returns the current context.

Xchat::set_context( $context | $channel,[$server] )
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

-  **$context** - context value as returned from ``get_context``,
   ``find_context`` or one of the fields in the list of hashrefs
   returned by ``list_get``
-  **$channel** - name of a channel you want to switch context to
-  **$server** - name of a server you want to switch context to

See ``find_context`` for more details on ``$channel`` and ``$server``.

Returns true on success, false on failure.

Xchat::get_info( $id )
~~~~~~~~~~~~~~~~~~~~~~~~~~

-  **$id** - one of the following case sensitive values

.. raw:: html

   <table border="1">   <tr style="background-color: #dddddd">
         <td>

ID

.. raw:: html

   </td>
         <td>

Return value

.. raw:: html

   </td>
         <td>

Associated Command(s)

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

away

.. raw:: html

   </td>
         <td>

away reason or undef if you are not away

.. raw:: html

   </td>
         <td>

AWAY, BACK

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

channel

.. raw:: html

   </td>
         <td>

current channel name

.. raw:: html

   </td>
         <td>

SETTAB

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

charset

.. raw:: html

   </td>
         <td>

character-set used in the current context

.. raw:: html

   </td>
         <td>

CHARSET

.. raw:: html

   </td>
      </tr>
   <tr>
      <td>

configdir

.. raw:: html

   </td> <td>

HexChat config directory encoded in UTF-8. Examples:
/home/user/.config/hexchat C:

.. raw:: html

   </td>
      <td></td>
   </tr>
   <tr>
         <td>

event\_text <Event Name>

.. raw:: html

   </td> <td>

text event format string for <Event name> Example:

.. raw:: html

   <div class="example synNormal"><div class='line_number'>
   <div>

1

.. raw:: html

   </div>
   </div>
   <div class='content'><pre><span class="synStatement">my</span> <span class="synIdentifier">$channel_msg_format</span> = Xchat::get_info( <span class="synStatement">&quot;</span><span class="synConstant">event_text Channel Message</span><span class="synStatement">&quot;</span> );
   </pre></div>
   </div>
      </td>
      <td></td>
   </tr>
   <tr>
      <td>

host

.. raw:: html

   </td>
      <td>

real hostname of the current server

.. raw:: html

   </td>
      <td></td>
   </tr><tr>
      <td>

id

.. raw:: html

   </td>
      <td>

connection id

.. raw:: html

   </td>
      <td></td>
   </tr><tr>
      <td>

inputbox

.. raw:: html

   </td>
      <td>

contents of the inputbox

.. raw:: html

   </td>
      <td>

SETTEXT

.. raw:: html

   </td>
   </tr><tr>
      <td>

libdirfs

.. raw:: html

   </td>
      <td>

the system wide directory where xchat will look for plugins. this string
is in the same encoding as the local file system

.. raw:: html

   </td>
      <td></td>
   </tr><tr>
      <td>

modes

.. raw:: html

   </td>
      <td>

the current channels modes or undef if not known

.. raw:: html

   </td>
      <td>

MODE

.. raw:: html

   </td>
   </tr><tr>
      <td>

network

.. raw:: html

   </td>
      <td>

current network name or undef, this value is taken from the Network List

.. raw:: html

   </td>
      <td></td>
   </tr><tr>
      <td>

nick

.. raw:: html

   </td>
      <td>

current nick

.. raw:: html

   </td>
      <td>

NICK

.. raw:: html

   </td>
   </tr><tr>
      <td>

nickserv

.. raw:: html

   </td>
      <td>

nickserv password for this network or undef, this value is taken from
the Network List

.. raw:: html

   </td>
      <td></td>
   </tr><tr>
      <td>

server

.. raw:: html

   </td>   <td>

current server name (what the server claims to be) undef if not
connected

.. raw:: html

   </td>
      <td></td>
   </tr><tr>
      <td>

state\_cursor

.. raw:: html

   </td>
      <td>

current inputbox cursor position in characters

.. raw:: html

   </td>
      <td>

SETCURSOR

.. raw:: html

   </td>
   </tr><tr>
      <td>

topic

.. raw:: html

   </td>
      <td>

current channel topic

.. raw:: html

   </td>
      <td>

TOPIC

.. raw:: html

   </td>
   </tr><tr>
      <td>

version

.. raw:: html

   </td>
      <td>

xchat version number

.. raw:: html

   </td>
      <td></td>
   </tr><tr>
      <td>

win\_status

.. raw:: html

   </td>
      <td>

status of the xchat window, possible values are "active", "hidden" and
"normal"

.. raw:: html

   </td>
      <td>

GUI

.. raw:: html

   </td>
   </tr><tr>
     <td>

win\_ptr

.. raw:: html

   </td> <td>

native window pointer, GtkWindow \* on Unix, HWND on Win32. On Unix if
you have the Glib module installed you can use my $window =
Glib::Object->new\_from\_pointer( Xchat::get\_info( "win\_ptr" ) ); to
get a Gtk2::Window object. Additionally when you have detached tabs,
each of the windows will return a different win\_ptr for the different
Gtk2::Window objects. See char\_count.pl for a longer example of a
script that uses this to show how many characters you currently have in
your input box.

.. raw:: html

   </td>
     <td></td>
   </tr>
   <tr>
     <td>

gtkwin\_ptr

.. raw:: html

   </td>
     <td>

similar to win\_ptr except it will always be a GtkWindow \*

.. raw:: html

   </td>
     <td></td>
   </tr>
   </table>

This function is used to retrieve certain information about the current
context. If there is an associated command then that command can be used
to change the value for a particular ID.

Xchat::get_prefs( $name )
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

-  **$name** - name of a HexChat setting (available through the /set
   command)

This function provides a way to retrieve HexChat's setting information.

Returns ``undef`` if there is no setting called called ``$name``.

Xchat::emit_print( $event, LIST )
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

-  **$event** - name from the Event column in *Settings* ``->`` *Text
   Events*
-  **LIST** - this depends on the Description column on the bottom of
   *Settings* ``->`` *Text Events*

This functions is used to generate one of the events listed under
*Settings* ``->`` *Text Events*.

Note: when using this function you **must** return ``Xchat::EAT_ALL``
otherwise you will end up with duplicate events. One is the original and
the second is the one you emit.

Returns true on success, false on failure.

Xchat::send_modes( $target | \@targets, $sign, $mode, [ $modes_per_line ] )
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

-  **$target** - a single nick to set the mode on
-  **\@targets** - an array reference of the nicks to set the mode
   on
-  **$sign** - the mode sign, either '+' or '-'
-  **$mode** - the mode character such as 'o' and 'v', this can only
   be one character long
-  **$modes_per_line** - an optional argument maximum number of modes
   to send per at once, pass 0 use the current server's maximum
   (default)

Send multiple mode changes for the current channel. It may send multiple
MODE lines if the request doesn't fit on one.

Example:

.. code-block:: perl

   use strict;
   use warning;
   use Xchat qw(:all);

   hook_command( "MODES", sub {
      my (undef, $who, $sign, $mode) = @{$_[0]};
      my @targets = split /,/, $who;
      if( @targets > 1 ) {
         send_modes( \@targets, $sign, $mode, 1 );
      } else {
         send_modes( $who, $sign, $mode );
      }
      return EAT_XCHAT;
   });

Xchat::nickcmp( $nick1, $nick2 )
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

-  **$nick1, $nick2** - the two nicks or channel names that are to be
   compared

The comparsion is based on the current server. Either an
`RFC1459 <http://www.ietf.org/rfc/rfc1459.txt>`_ compliant string
compare or plain ascii will be using depending on the server. The
comparison is case insensitive.

Returns a number less than, equal to or greater than zero if ``$nick1``
is found respectively, to be less than, to match, or be greater than
``$nick2``.

Xchat::get_list( $name )
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

-  **$name** - name of the list, one of the following: "channels",
   "dcc", "ignore", "notify", "users"

This function will return a list of hash references. The hash references
will have different keys depend on the list. An empty list is returned
if there is no such list.

"channels" - list of channels, querys and their server

.. raw:: html

   <table border="1">   <tr style="background-color: #dddddd">
         <td>

Key

.. raw:: html

   </td>   <td>

Description

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

channel

.. raw:: html

   </td>  <td>

tab name

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

chantypes

.. raw:: html

   </td>
         <td>

channel types supported by the server, typically "#&"

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

context

.. raw:: html

   </td>  <td>

can be used with set\_context

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

flags

.. raw:: html

   </td> <td>

Server Bits: 0 - Connected 1 - Connecting 2 - Away 3 - EndOfMotd(Login
complete) 4 - Has WHOX 5 - Has IDMSG (FreeNode)

.. raw:: html

   <p>

The following correspond to the /chanopt command

.. raw:: html

   </p>
                       

6 - Hide Join/Part Message (text\_hidejoinpart) 7 - unused (was for
color paste) 8 - Beep on message (alert\_beep) 9 - Blink Tray
(alert\_tray) 10 - Blink Task Bar (alert\_taskbar)

.. raw:: html

   <p>

Example of checking if the current context has Hide Join/Part messages
set:

.. raw:: html

   </p>
   <div class="example synNormal"><div class='line_number'>
   <div>

1

.. raw:: html

   </div>
   <div>

2

.. raw:: html

   </div>
   <div>

3

.. raw:: html

   </div>
   </div>
   <div class='content'><pre><span class="synStatement">if</span>( Xchat::context_info-&gt;{flags} &amp; (<span class="synConstant">1</span> &lt;&lt; <span class="synConstant">6</span>) ) {
     Xchat::<span class="synStatement">print</span>( <span class="synStatement">&quot;</span><span class="synConstant">Hide Join/Part messages is enabled</span><span class="synStatement">&quot;</span> );
   }
   </pre></div>
   </div>                     </td>
      </tr>   <tr>
         <td>

id

.. raw:: html

   </td> <td>

Unique server ID

.. raw:: html

   </td>
      </tr>

.. raw:: html

   <tr>
         <td>

lag

.. raw:: html

   </td>
         <td>

lag in milliseconds

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

maxmodes

.. raw:: html

   </td> <td>

Maximum modes per line

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

network

.. raw:: html

   </td>  <td>

network name to which this channel belongs

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

nickprefixes

.. raw:: html

   </td>   <td>

Nickname prefixes e.g. "+@"

.. raw:: html

   </td>
      </tr>

.. raw:: html

   <tr>
         <td>

nickmodes

.. raw:: html

   </td>   <td>

Nickname mode chars e.g. "vo"

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

queue

.. raw:: html

   </td>
         <td>

number of bytes in the send queue

.. raw:: html

   </td>
      </tr>

.. raw:: html

   <tr>
         <td>

server

.. raw:: html

   </td>   <td>

server name to which this channel belongs

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

type

.. raw:: html

   </td>  <td>

the type of this context 1 - server 2 - channel 3 - dialog 4 - notices 5
- server notices

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

users

.. raw:: html

   </td> <td>

Number of users in this channel

.. raw:: html

   </td>
      </tr>
   </table>

"dcc" - list of DCC file transfers

.. raw:: html

   <table border="1">   <tr style="background-color: #dddddd">
         <td>

Key

.. raw:: html

   </td>   <td>

Value

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

address32

.. raw:: html

   </td>   <td>

address of the remote user(ipv4 address)

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

cps

.. raw:: html

   </td>   <td>

bytes per second(speed)

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

destfile

.. raw:: html

   </td> <td>

destination full pathname

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

file

.. raw:: html

   </td>  <td>

file name

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

nick

.. raw:: html

   </td>
         <td>

nick of the person this DCC connection is connected to

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

port

.. raw:: html

   </td>  <td>

TCP port number

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

pos

.. raw:: html

   </td>   <td>

bytes sent/received

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

poshigh

.. raw:: html

   </td>   <td>

bytes sent/received, high order 32 bits

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

resume

.. raw:: html

   </td>   <td>

point at which this file was resumed (zero if it was not resumed)

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

resumehigh

.. raw:: html

   </td>   <td>

point at which this file was resumed, high order 32 bits

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

size

.. raw:: html

   </td>  <td>

file size in bytes low order 32 bits

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

sizehigh

.. raw:: html

   </td> <td>

file size in bytes, high order 32 bits (when the files is > 4GB)

.. raw:: html

   </td>
       </tr>
       <tr>
         <td>

status

.. raw:: html

   </td>   <td>

DCC Status: 0 - queued 1 - active 2 - failed 3 - done 4 - connecting 5 -
aborted

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

type

.. raw:: html

   </td>  <td>

DCC Type: 0 - send 1 - receive 2 - chatrecv 3 - chatsend

.. raw:: html

   </td>
      </tr></table>

"ignore" - current ignore list

.. raw:: html

   <table border="1">   <tr style="background-color: #dddddd">
         <td>

Key

.. raw:: html

   </td> <td>

Value

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

mask

.. raw:: html

   </td>  <td>

ignore mask. e.g: *!*\ @\*.aol.com

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

flags

.. raw:: html

   </td> <td>

Bit field of flags. 0 - private 1 - notice 2 - channel 3 - ctcp 4 -
invite 5 - unignore 6 - nosave 7 - dcc

.. raw:: html

   </td>
      </tr></table>

"notify" - list of people on notify

.. raw:: html

   <table border="1">
      <tr style="background-color: #dddddd">
         <td>

Key

.. raw:: html

   </td>   <td>

Value

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

networks

.. raw:: html

   </td>
         <td>

comma separated list of networks where you will be notfified about this
user's online/offline status or undef if you will be notificed on every
network you are connected to

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

nick

.. raw:: html

   </td>  <td>

nickname

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

flags

.. raw:: html

   </td> <td>

0 = is online

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

on

.. raw:: html

   </td> <td>

time when user came online

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

off

.. raw:: html

   </td>   <td>

time when user went offline

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

seen

.. raw:: html

   </td>  <td>

time when user was last verified still online

.. raw:: html

   </td>
      </tr>
   </table>

The values indexed by on, off and seen can be passed to localtime and
gmtime, see perldoc -f
`localtime <http://perldoc.perl.org/functions/localtime.html>`_ and
perldoc -f `gmtime <http://perldoc.perl.org/functions/gmtime.html>`_ for
more details.

"users" - list of users in the current channel

.. raw:: html

   <table border="1">
   <tr style="background-color: #dddddd">
         <td>

Key

.. raw:: html

   </td>   <td>

Value

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

away

.. raw:: html

   </td>  <td>

away status(boolean)

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

lasttalk

.. raw:: html

   </td>
         <td>

last time a user was seen talking, this is the an epoch time(number of
seconds since a certain date, that date depends on the OS)

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

nick

.. raw:: html

   </td>  <td>

nick name

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

host

.. raw:: html

   </td>
         <td>

host name in the form: user@host or undef if not known

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

prefix

.. raw:: html

   </td>   <td>

prefix character, .e.g: @ or +

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

realname

.. raw:: html

   </td>
          <td>

Real name or undef

.. raw:: html

   </td>
      </tr>   <tr>
         <td>

selected

.. raw:: html

   </td>
         <td>

selected status in the user list, only works when retrieving the user
list of the focused tab. You can use the /USELECT command to select the
nicks

.. raw:: html

   </td>
      </tr>
   </table>

"networks" - list of networks and the associated settings from network
list

.. raw:: html

   <table border="1">   <tr style="background-color: #dddddd">
         <td>

Key

.. raw:: html

   </td>   <td>

Value

.. raw:: html

   </td>
      </tr>

::

    <tr>
    <td>autojoins</td> <td>An object with the following methods:<br />
        <table>
            <tr>
                <td>Method</td>
                <td>Description</td>
            </tr>           <tr>
                <td>channels()</td>
                <td>returns a list of this networks' autojoin channels in list context, a count of the number autojoin channels in scalar context</td>
            </tr>           <tr>
                <td>keys()</td>
                <td>returns a list of the keys to go with the channels, the order is the same as the channels, if a channel doesn't  have a key, '' will be returned in it's place</td>
            </tr>           <tr>
                <td>pairs()</td>
                <td>a combination of channels() and keys(), returns a list of (channels, keys) pairs. This can be assigned to a hash for a mapping from channel to key.</td>
            </tr>           <tr>
                <td>as_hash()</td>
                <td>return the pairs as a hash reference</td>
            </tr>           <tr>
                <td>as_string()</td>
                <td>the original string that was used to construct this autojoin object, this can be used with the JOIN command to join all the channels in the autojoin list</td>
            </tr>           <tr>
                <td>as_array()</td>
                <td>return an array reference of hash references consisting of the keys "channel" and "key"</td>
            </tr>           <tr>
                <td>as_bool()</td>
                <td>returns true if the network has autojoins and false otherwise</td>
            </tr>
        </table>
    </td>
    </tr>

    <tr>
    <td>connect_commands</td> <td>An array reference containing the connect commands for a network. An empty array if there aren't any</td>
    </tr>   <tr>
    <td>encoding</td> <td>the encoding for the network</td>
    </tr>   <tr>
        <td>flags</td>
        <td>
            a hash reference corresponding to the checkboxes in the network edit window
            <table>
                <tr>
                    <td>allow_invalid</td>
                    <td>true if "Accept invalid SSL certificate" is checked</td>
                </tr>               <tr>
                    <td>autoconnect</td>
                    <td>true if "Auto connect to this network at startup" is checked</td>
                </tr>               <tr>
                    <td>cycle</td>
                    <td>true if "Connect to selected server only" is <strong>NOT</strong> checked</td>
                </tr>               <tr>
                    <td>use_global</td>
                    <td>true if "Use global user information" is checked</td>
                </tr>               <tr>
                    <td>use_proxy</td>
                    <td>true if "Bypass proxy server" is <strong>NOT</strong> checked</td>
                </tr>               <tr>
                    <td>use_ssl</td>
                    <td>true if "Use SSL for all the servers on this network" is checked</td>
                </tr>
            </table>
        </td>
    </tr>   <tr>
        <td>irc_nick1</td>
        <td>Corresponds with the "Nick name" field in the network edit window</td>
    </tr>   <tr>
        <td>irc_nick2</td>
        <td>Corresponds with the "Second choice" field in the network edit window</td>
    </tr>   <tr>
        <td>irc_real_name</td>
        <td>Corresponds with the "Real name" field in the network edit window</td>
    </tr>   <tr>
        <td>irc_user_name</td>
        <td>Corresponds with the "User name" field in the network edit window</td>
    </tr>   <tr>
        <td>network</td>
        <td>Name of the network</td>
    </tr>   <tr>
        <td>nickserv_password</td>
        <td>Corresponds with the "Nickserv password" field in the network edit window</td>
    </tr>   <tr>
        <td>selected</td>
        <td>Index into the list of servers in the "servers" key, this is used if the "cycle" flag is false</td>
    </tr>   <tr>
        <td>server_password</td>
        <td>Corresponds with the "Server password" field in the network edit window</td>
    </tr>   <tr>
        <td>servers</td>
        <td>An array reference of hash references with a "host" and "port" key. If a port is not specified then 6667 will be used.</td>
    </tr>

.. raw:: html

   </table>

Xchat::user_info( [$nick] )
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

-  **$nick** - the nick to look for, if this is not given your own
   nick will be used as default

This function is mainly intended to be used as a shortcut for when you
need to retrieve some information about only one user in a channel.
Otherwise it is better to use ``get_list``. If ``$nick`` is found a hash
reference containing the same keys as those in the "users" list of
``get_list`` is returned otherwise undef is returned. Since it relies on
``get_list`` this function can only be used in a channel context.

Xchat::context_info( [$context] )
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

-  **$context** - context returned from ``get_context``,
   ``find_context`` and ``get_list``, this is the context that you want
   infomation about. If this is omitted, it will default to current
   context.

This function will return the information normally retrieved with
``get_info``, except this is for the context that is passed in. The
information will be returned in the form of a hash. The keys of the hash
are the ``$id`` you would normally supply to ``get_info`` as well as all
the keys that are valid for the items in the "channels" list from
``get_list``. Use of this function is more efficient than calling
get\_list( "channels" ) and searching through the result.

Example:

.. code-block:: perl

   use strict;
   use warnings;
   use Xchat qw(:all); # imports all the functions documented on this page

   register( "User Count", "0.1",
      "Print out the number of users on the current channel" );
   hook_command( "UCOUNT", \&display_count );
   sub display_count {
      prnt "There are " . context_info()->{users} . " users in this channel.";
      return EAT_XCHAT;
   }

Xchat::strip_code( $string )
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

-  **$string** - string to remove codes from

This function will remove bold, color, beep, reset, reverse and
underline codes from ``$string``. It will also remove ANSI escape codes
which might get used by certain terminal based clients. If it is called
in void context ``$string`` will be modified otherwise a modified copy
of ``$string`` is returned.

Examples
--------

Asynchronous DNS resolution with hook\_fd
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: perl

   use strict;
   use warnings;
   use Xchat qw(:all);
   use Net::DNS;

   hook_command( "BGDNS", sub {
      my $host = $_[0][1];
      my $resolver = Net::DNS::Resolver->new;
      my $sock = $resolver->bgsend( $host );

      hook_fd( $sock, sub {
         my $ready_sock = $_[0];
         my $packet = $resolver->bgread( $ready_sock );

         if( $packet->authority && (my @answers = $packet->answer ) ) {

            if( @answers ) {
               prnt "$host:";
               my $padding = " " x (length( $host ) + 2);
               for my $answer ( @answers ) {
                  prnt $padding . $answer->rdatastr . ' ' . $answer->type;
               }
            }
         } else {
            prnt "Unable to resolve $host";
         }

         return REMOVE;
      },
      {
         flags => FD_READ,
      });

      return EAT_XCHAT;
   });

Contact Information
-------------------

Contact Lian Wan Situ at <atmcmnky [at] yahoo.com> for questions,
comments and corrections about this page or the Perl plugin itself. You
can also find me in #xchat on freenode under the nick Khisanth.

.. raw:: html

   <table border="0" width="100%" cellspacing="0" cellpadding="3">
   <tr><td class="block" style="background-color: #cccccc" valign="middle">

 X-Chat 2 Perl Interface

.. raw:: html

   </td></tr>
   </table>


