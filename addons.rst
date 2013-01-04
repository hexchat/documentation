Addons
======

Do At
-----

This plugin can be used to perform an arbitrary command on multiple channels or contexts. List can be specific to networks, or any context on a separate network. Usage:

:command:`/doat [channel,list,/network] [command]`

To change username on network FreeNode from some other network:

:command:`/doat /FreeNode nick ANewNick`


Exec
----

With Exec you can perform commands as if you ran them in the command line. The output will be printed at once, in the end of execution. If the command takes more than 10 seconds to complete, it gets aborted to avoid locking down HexChat. Usage:

:command:`/exec ping google.com`


FISHLiM
-------

Adds FiSH encryption support.  You set a password for a conversation/channel, and then all your messages get encrypted. Only those who know the password will be able to read your messages. Usage is simple: first you set the password:

:command:`/setkey yoursecretkey`

Then you let your fellow chatters know this password. Once they also set the password on their sides, they'll receive messages decrypted and send them encrypted, too.


Update Checker
--------------

Automatically checks for updates available. Can be manually checked via *Help -> Check for Updates* or by the command:

:command:`/updcheck`


Sysinfo
-------

Prints out basic system information on both Windows and Unix. You can either activate in *Window -> Display System Info* or type:

:command:`/sysinfo`


Checksum
--------

Automatically calculates the SHA-256 checksum of files sent and recieved via DCC.
