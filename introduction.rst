IRC INTRODUCTION
================

**Contents**

* `Basics`_
* `Important Terms`_

  * `Networks`_
  * `Servers`_
 
    * `Networks vs. Servers`_
    
  * `Channels`_
 
* `Upper/Lower Case`_
* `Example`_
* `USING IRC`_
* `Pitfalls to watch out for`_
* `Wrap up`_

*Last updated: November 11, 2020*

----

_`Basics`
---------

**Internet Relay Chat, or IRC for short, is for group conversations**.

IRC is a mature, still widely used, global set of computer systems, to allow people to chat with others of like mind, on a wide variety of subjects.
For example there are many thousands of ongoing conversations about various aspects of computers, politics, religion, science, etc. happening every day on IRC.

Each group conversation, is between a different group of people:

* To chat on an existing *Topic*, you can "/join" one or more conversations of your choice.
* Or you can start your own Topic of conversation, and invite others to join you.

**TL;DR: To use IRC successfully you might not need to read what follows.**  
This is written for both the novice and for experts, (who also get confused about things from time to time).

For everyday use, all IRC is similar.
But there's *not* only one common form of IRC.
Rather there are many, slightly different user interfaces, depending on how it's installed.

IRC is not from any one company or organization.  
Rather most, if not all IRC software, is in the public domain, and the most common sites where you use it are free.

Because IRC has been around for a long time, in different forms, there can be too much information to easily boil down.
I'm going to provide you with a quick, basic understanding of it's overall structure.
I think you'll find IRC to be fairly simple to use.

I won't go into how to use the HexChat IRC client; we'll get to that next.  Nor will I cover how to run an IRC server.

After you understand the basics, you might want to refer to this `Wikipedia article <https://en.wikipedia.org/wiki/Internet_Relay_Chat>`_.

*The more I learn about IRC, the more exciting it is.*

----

_`Important Terms`
------------------

What some call an **IRC Network**, others call an **IRC Server**, but it's important to distinguish between these terms.

Please also see the example below to help you get a better feel of how Networks and Servers relate to each other.

----

_`Networks`
+++++++++++

The term "Networking", in general,
  means *working together with your friends toward a common goal*.

A computer Network, is similar.
  It's a group of computers that are connected together to collectively provide solutions or information.

An IRC computer Network, or simply an IRC Network (singular), is like a company.
  It provides a continuous, non-stop 24/7, *convention-center* of sorts,
  focused on providing group conversations, on a variety of topics, to all those who attend.
  Some Networks focus on certain areas of interest, others support conversations more generally, often depending on who is running them.

The word "Network" might cause you to think that IRC is one big global network, like the internet.  It's not.
 
**IRC is a whole bunch of independent IRC Networks.**
These independent Networks don't talk to each other, nor share any network-to-network content, and
don't even necessarily use the same serving software, nor
authenticate with the same methods or "credentials" (i.e. using a name and password).
 
That said, modern IRC Networks are generally speaking, quite similar to each other, in that you can use most of the same commands and clients with them.
 
You'll be able to use HexChat to connect to multiple networks at the same time,
each with a different set of users, and thus carry on multiple conversations at the same time.
 
As used here, the term *IRC Network* refers to the fact that you can chat with lots of other people just like you,
who are also connected to the same Network you're connected to.
 
In other words, the term *Network* does NOT refer to the set of all IRC Networks.
You might instead call that *The Entire IRC Universe*, as there are currently `over 400 separate IRC Networks <https://netsplit.de/networks/>`_.


----

_`Servers`
++++++++++

The term "Serving", in general,
  means "to do what you ask".
  A Server is like a waiter in a restaurant, where he or she brings what you ask for, and takes away what needs to be removed from your table.

A computer server,
  is a computer that is running special software "to serve you", meaning to do what you say, in terms of
 
  * accepting commands,
  * sending you stuff you asked for, and
  * receiving stuff from you with instructions of what to do with it.

An IRC Server,
  is a computer server owned by an IRC Network, *which takes what one person said, and makes sure everyone else in the group heard it*,
  thus creating a group conversation.
 
  Sometimes the term "Server" can refer to a group of Servers (plural), who all together do the same thing, but are able to serve more people at the same time.

Server ownership
  A given Server is owned by, and only works for a given Network.
 
  The computers (aka machines) you actually connect to, and then send and receive messages to and from, are actual the Network's own individual Servers.

Connecting to a server
  Servers are each connected to the internet and therefore each have an IP address, though you may never see these.
  You connect to them via TCP (The Internet's Transmission Control Protocol, to make sure that what is sent is accurately received).
 
  Each Network has a standard URI (Uniform Resource Identifier) style name used to connect to it's servers, for example **irc.SpotChat.org**.

  For *freenode* it's: "*chat.freenode.net:6697 / non-SSL: 6667*", where the trailing colon-number is an explicit TCP port number.
  That means connect to **chat.freenode.net:6697** for a secure communications connection to their server,
  or to **chat.freenode.net:6667** for an insecure connection.
 
  A secure connection via port 6697 helps protect any passwords you send to the server.
 
  Security for port 6697 is provided by the SSL (Secure Socket Layer) protocol.
  (Note that because of an uncovered defect in the SSL encryption package a few years back,
  SSL now actually provides TLS (Transport Layer Security) protocol instead of the older broken SSL protocol.
  For compatibility however, it's still called "SSL", which is a bit confusing.
  So in other words, they changed how it works, but not the name of it.  This fixed the defect, without breaking stuff that already used it.)

  **Caution!** Just because your own Server connection is secure, **don't assume that you have a secure end-to-end connection.**
  What you tell the server securely, can then be shared across insecure connections to other users who are not connected securely.

Server software = A running daemon
  Each IRC Server is a computer running the IRC daemon [#]_ software.
  This is a program to offer IRC group conversations in what are called "Channels" (aka "chat rooms") offered by that Network.
  (More on Channels below.)
 
Multiple servers for a given Network
  Multiple servers from one particular Network, can all communicate with each other.  And Servers from different Networks, don't communicate.

  A Network must, at a minimum, have at least one Server, but it also might have two or more to help share the load, or for other reasons.
  When a given Network runs multiple Servers, they all serve roughly the same information.
 
  For each Network I'm using, I'm actually connected to only one of it's Servers at any given time.
  When I send a message to my server for the group to see, it shares my message with all its servers for that network, so my message is sent
  to everyone connected and listening for messages.
 
  It normally doesn't matter which server you connect to (or thru) for a given Network, as they all serve the same stuff.
  But some of the Servers might be near and others distant, thus some faster and others slower,
  or some servers might have more or less privacy.
 
----

_`Networks vs. Servers`
++++++++++++++++++++++

Therefor an *IRC Network* like *freenode*, with it's own unique URL on the internet,
can consist of one or several *IRC Servers*, each with their own unique IP (Internet Protocol) address.

Note that a Network could have just one server.
For example, the Network named *SpotChat*, uses the Server with the URL of *irc.spotchat.org*.
 
If the Network-to-Server setup is one-to-one like this, with only one server, then this Server can be said to refer to it's Network,
instead of the normal other way around.
Thus the blur between the terms Network and Server.
 
Remember, the Network is just a simple text name, like *freenode*, and also usually has an associated domain name or URL, like *irc.freenode.org* used to reach it's Servers.
 
A given Network's web site, or your friend, might suggest one or more explicit Servers to use for that Network.
However if your only given a Server's URL, for example *irc.SpotChat.org*,
you can usually fairly easily deduce the Network name to be just the Domain name, in this case: *SpotChat*.

Just remember that:
 
* Servers do the actual work.
* Networks own servers.

----

_`Channels`
+++++++++++

A Channel
  is a group conversation with a specific Topic of discussion, where like-minded people can gather and chat with each other.
 
  What some people refer to as a *Chat room*, others call a *Channel*.
  To quote a noteworthy source: "`Chat rooms on IRC are usually called Channels. <https://netsplit.de/channels/>`_"

In the example below, I am "Connected" (via TCP over the Internet) to four different Networks at the same time,
and these each provide a variety of Channels to use.
Each Channel's conversations are kept in a separate window.
 
The reason you have to connect to different Networks, is because you have to go where the Channels you want are served,
unlike with the internet for example, where everything can be reached from your on-ramp (i.e. your default *gateway*).
 
For example, some of the Channels I want, might be found on Network 1, and some on Network 2.

Notice that two of the Networks below both provide the Channel named **#test**.
These are two different, and unrelated Channels named #test.
Even though they have the same name they are on different Networks.
These #test channels are used for learning, and so if you make a mistake you don't bother other users unnecessarily.
 
Also the prefix "**#**" (hash) symbol is just a normal character that is part of the channel name used to help people see that it's a channel name.
(There is even a channel on the OFTC Network named just simply **#**, but with the humorous topic of,
"In God we trust. Everybody else, hands where I can see them". -lol)
 
**So remember that each different network serves different Channels, even when their channel names are identical.**
(For example, a Channel named *#debian* can exist on more than one Network, but these are two completely independent Channels.
They don't share users, nor their comments, and again they don't share the same name registration and login credentials either.)

If you don't already know a Channel you want to use, or if you can't remember which Network it's on,
you can search across multiple Networks for channels at an IRC Search Engine like this one: https://netsplit.de/channels/ .

----

_`Upper/Lower Case`
-------

IRC is mostly case-insensitive.  For example, the Channel names '#TEST', '#Test' and '#test' are the same.

Passwords however, are still case sensitive.

----


_`Example`
----------

This is a typical example of you connecting to four *Networks* to be able to chat on 7 channels (that is, in 7 different windows or tabs on your screen, each with a different, group conversation in it).

* Each Network in the example below has one or two *Servers* (i.e. one or two internet server addresses).  These serve clients of that *Network*.
* And each of these *Networks* (via their Server's hardware) hosts one or more *Channels* where you can chat with others.

+------------+-------------------+-------------------+
| Network 1  | Server addresses  | Channel(s) joined |
+============+===================+===================+
| **OFTC**                                           |
+------------+---------------------------------------+
|            | irc.oftc.net                          |
+------------+-------------------+-------------------+
|                                | #debian           |
+------------+-------------------+-------------------+

----

+------------+-------------------+-------------------+
| Network 2  | Server addresses  | Channel(s) joined |
+============+===================+===================+
| **SpotChat**                                       |
+------------+---------------------------------------+
|            | irc.spotchat.org                      |
+------------+-------------------+-------------------+
|                                | | #linuxmint-help |
|                                | #test             |
+------------+-------------------+-------------------+

----

+------------+-------------------+-------------------+
| Network 3  | Server addresses  | Channel(s) joined |
+============+===================+===================+
| **GIMPNet**                                        |
+------------+---------------------------------------+
|            | | irc.gimp.org                        |
|            | irc.gnome.org                         |
+------------+-------------------+-------------------+
|                                | #anjuta           |
+------------+-------------------+-------------------+

----

+------------+-------------------+-------------------+
| Network 4  | Server addresses  | Channel(s) joined |
+============+===================+===================+
| **freenode**                                       |
+------------+---------------------------------------+
|            | | chat.freenode.net:6697 (uses SSL)   |
|            | chat.freenode.net:6667 (non-SSL)      |
+------------+-------------------+-------------------+
|                                | | #hexchat        |
|                                | | #test           |
|                                | | #kdevelop       |
|                                | etc.              |
+--------------------------------+-------------------+

In the example above, the 'freenode' network has two server choices described as a URL with_a_port# (separated by a colon).
These URLs are the internet domain names used to locate the actual freenode IRC servers on the internet.

* For privacy use port 6697 (with a '9' in it) which uses an encrypted connection to the server (not to other users).
* If you must use port 6667 (with a '67' in it) **then everything you say possibly including your password is visible to internet snoopers**.


----

_`USING IRC`
------------

To chat on IRC,

* **First identify one or more IRC Networks to use, which serve the Channel (or Channel's) you want to chat in**.
 
* **Then you'll need a name for yourself.**  Pay attention here; it's confusing at first.
 
  Actually for each Network there are three different *types* of user names you can have.
  They are your **Nick**, **Real**, and **User** names for that Network.
  This seems a little confusing, but a **Nick**, (also known as a **NickName**), seems to be *the only name that really matters*.
 
  Your NickName, is supplied *to a specific Network*, (not to a specific Channel, nor to all IRC Networks).
 
  Your NickName is the name that other users (other clients like you) on that Network see, when you /join a Channel (to be able to chat there),
  and it's the name they see next to comments you post in that Channel.  (All IRC commands when entered manually have a leading slash (/).)
 
  Also if other users want an IRC message to go directly to you, and you alone, it's the name they use to address their message to you.

  Note that **you might use the same, or different, NickNames on each different Network you connect to**.
  So your NickName on the freenode Network might be *foo*, and your NickName on the SpotChat Network could also be *foo*, but it could also be *foobar* there.
 
  **A given NickName must be unique to a given Network**, so you can't use a NickName that's already in use on that Network by another person.
  In other words, there can't be two user's named "foo" on the freenode Network.
  But there can be user named "foo" on the freenode Network, and also a user named "foo" on the SpotChat Network.  You get the idea.

  You might *want* to use the same NickNames on all IRC Networks you connect to, but beware,
  as there is no central or global IRC name registry for all IRC Networks, like with Domain Names for the Internet.
  You might use for example, "foo" on freenode, and then try to create "foo" on SpotChat only to find that someone else is using it there.

  *So you might have to use different NickNames at different Networks.*
  This is a good reason to choose unique names if you can.
  This is also be the reason HexChat requests that you supply three possible NickNames, a first, second and third name choice.
 
* You set your NickName on a Network by using the '/NICK <nickname>', or /nick <nickname> command,
  (or HexChat can automatically do this for you if properly configured).
 
* If you want to be more anonymous you can change your NickName often.
 
* Or, you can *register a NickName* at a given Network, and permanently hold on to it.
  This makes is so other users can't officially use your registered NickName.
 
  This is partly so others can rely on the fact that that particular name is being used by you, and not by someone else.
 
  More importantly, however, is the fact that some Networks or Channels require you to first confirm
  *it's actually you using your NickName, and not someone else using it*
  before you can join or chat on one or more Channels.
  This is to help control abusive behavior by anonymous users.
 
* **To permanently registered a NickName**, for a given Network, you use a special server on each network called *NickServ*
  (short for the NickName registration Server).
  You use NickServ to both set or change your password, and to later allow you to use that NickName, you supply your password.
  HexChat can store these details to make all of this much easier than it sounds, but you will need to know what's going on to effectively set it up.
 
  Remember, you register to a Network.  So then if you use a different Network, you might also need to register again there for that Network.
 
  Registration is done by first registering your *NickName* with a new password and your verified email address,
  and then later re-verifying (**they call it 'Identifying'**) using your NickName and password to authenticate you as owning that NickName.
 
  Your email address is needed for registration, and it can be updated later if necessary without loosing your registration.

 
* **IMPORTANT!**  If you do register your NickName with a password, just remember to use one password with a given Network, and another,
  different password with a second Network, and keep them straight so you don't get confused, (as your NickName might be the same on both Networks).
 
  For example::
 
    Network 1 - NickName a - password i
    Network 2 - NickName a - password ii
    network 3 - NickName B - password iii
    ...


* Finally your **User Name** and **Real Name** (both with a space in them) don't really matter, and can be changed at every log in.
  They aren't much used, and they don't even seem to be stored by the server.
 
  Your **User Name** is simply the user part in your user@host *hostmask* that appears to others on IRC, showing where your connection originates from.
  In the early days of IRC it was typical for people to run their IRC client on multi-user computers,
  and the username corresponded to their local username on that computer.
  Some people do still use IRC from multi-user shell servers, but for the most part the username is vestigial
  (meaning: remaining in a form that is small or imperfectly developed and not able to function).
  The username is not unique across the network and is not used to log you in.
  In HexChat set it in Menu> HexChat | Network list.

  The **Real Name** is used to populate the real name field that appears when someone uses the WHOIS command on your nick.
  Unlike the other two fields, this field can be fairly long and contain most characters (including spaces).
  Some people do put their real name here, but many do not.
  In HexChat set it with:  Menu> Preferences | Chatting | Advanced | Miscellaneous | Real name

----

_`Pitfalls to watch out for`
----------------------------

In general the IRC Networks are very similar, but be warned as you might find some commands that are in one Network, but not on another, and
there are also commands that behave differently on different Networks.
 
For example the optional parameters are reversed on these two different Networks:
 
* /msg NickServ IDENTIFY [<nickname>] <password>  <-- on the FreeNode Network
* /msg NickServ IDENTIFY <password> [<nickname>]  <-- on the OFTC Network
 
This can cause confusion when you're trying to login in (or rather, what they call *Identify*) using your password.
 
Fortunately, there aren't too many issues like this.

----

_`Wrap up`
----------

There's lots more to learn about IRC, but that should help get you started.
Don't sweat the fact that there are tons of commands out there (see /help), just try to watch for awhile before jumping in.

IRC Networks can be public or private.

When they are public, they sometimes ask for donations to help pay for them.
Please do you part to be respectful of those who have provided the servers, and pitch in to help if you can.

**Always be courteous and friendly, and be sure to read all notices and rules, as they vary between Networks.**

----

.. [#] A **daemon** is not an evil spirit, rather it is software that runs continuously on a multi-tasking computer [#]_,
  usually to offer a service of some sort by listening for commands and then responding appropriately.  
 
.. [#] A **multi-tasking computer** is a computer than can run multiple programs at roughly the same time.  
  It's as if each program separately owns the entire computer during the time when it is running.
