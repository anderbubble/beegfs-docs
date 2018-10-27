=====
Admon
=====

The BeeGFS Administration and Monitoring System (short: Admon)
provides a graphical interface to perform administrative management
tasks and to monitor the state of the file system and its components.

The BeeGFS Administration and Monitoring System consists of two parts:

- The Admon daemon, which can run on any machine with network access
  to the metadata and storage servers. This daemon gathers the status
  information of the other BeeGFS services and stores it in a
  database.

- The graphical Java-based client, which can run on your
  workstation. It connects to the remote Admon daemon via http.

.. note:: It is recommend to use `Oracle Java Runtime Environment 7
          <http://www.java.com/de/download/manual.jsp>`_ (formerly
          known as Sun JRE 7) or higher to run the BeeGFS Admon
          GUI. Other Java runtime environments may work, but are not
          fully tested.


Installation and basic Setup
============================

The Administration and Monitoring System for BeeGFS is contained in
the optional beegfs-admon package.

The package is available either from the general BeeGFS repository or
via direct download.

The package provides an init script to start the Admon daemon
(/etc/init.d/beegfs-admon) and a configuration file
(/etc/beegfs/beegfs-admon.conf).


.. note:: If you installed BeeGFS manually (i.e. not via the Admon
          GUI), you need to edit the beegfs-admon.conf file and set
          the parameter sysMgmtdHost to the hostname of your
          management server.

After installation, start the daemon::

  $ /etc/init.d/beegfs-admon start

The graphical user interface for BeeGFS Admon comes packaged together
with the Admon daemon and is located in /opt/beegfs/beegfs-admon-gui.


Admon GUI Start
===============

If your BeegFS Admon daemon is not running yet, start it as described
here: Admon Installation and basic setup

By default, TCP port 8000 will be opened by the daemon for HTTP
connections.

To get the GUI, point your browser to
http://Host_Where_The_Admon_Runs:8000 and download the jar-file from
there.

The GUI can be started by double clicking from a file browser or by
using the java -jar command (depending on your operating system and
configuration). If you want to run the GUI from its default location
on the Admon host, use the following command::

  $ java -jar /opt/beegfs/beegfs-admon-gui/beegfs-admon-gui.jar 


At first start, you will be prompted to provide the hostname and port
(default: 8000) of the host on which the Admon daemon is running. It
is also possible to change the resolution of the internal desktop of
the GUI and the default log level of the GUI.

.. figure: beegfs_admon_gui_conf.png

.. note:: It is recommend to use `Oracle Java Runtime Environment 7
          <http://www.java.com/de/download/manual.jsp>`_ (formerly
          known as Sun JRE 7) or higher to run the BeeGFS Admon
          GUI. Other Java runtime environments may work, but are not
          fully tested.


Admon Login
===========

The login mechanism is based on two predefined users.

The user "Information" (which has the initial password "information")
is only able to view statistics, whereas the user "Administrator"
(which has the initial password "admin") is also able to perform
administrative tasks.

It is highly recommended that the first thing you do is to log in
using the administrative account and change the predefined passwords.

.. figure: beegfs_admon_login_information.png

A user with administrative privileges can also turn off the need for
authentication for the informational user.


Admon Main Menu
===============

The menu is a tree-like view on the left hand side and the associated
windows will open on double-click.  The following sections will give a
short overview of the different items.


The menu is a tree-like view on the left hand side and the associated
windows will open on double-click.  The following sections will give a
short overview of the different items.


Metadata Nodes
--------------

The menu item "Metadata Nodes" contains an overview page, as well as a
dedicated page for each metadata node in the system.

The overview shows basic information, the status of all nodes and the
total number of metadata requests in the system.

The page for a specific metadata node shows some general information
on the node itself, the status of the node and the number of work
requests to this node.


Storage Nodes
-------------

Like the meta nodes menu, the menu item "Storage Nodes" also consists
of an overview page, as well as a dedicated page for each storage node
in the system.

Values which can be retrieved on these pages include the general
status information, as well as disk space usage and data throughput.

For disk performance, four values are displayed. While the read and
write graphs are very exact (measured every second), they are also
very erratic. The averaged graphs are better suited to identify a
tendency. These graphs are always an average of the last 30
values. You can easily hide each throughput line by disabling the
appropriate checkbox under the graphic.

Note that only the 10 min history view is based on the exact one
second interval. The other history views are based on more
coarse-grained (averaged) values.


Client statistics
-----------------

The pages contains the client statistics for metadata operations
(create, stat, ...) or the clients statistics for the storage
operations (read, write, ...).


User statistics
---------------

The pages contains the user statistics for metadata operations
(create, stat, ...) or the user statistics for the storage operations
(read, write, ...).


Management
----------

The management pages contain elements for administrative tasks. The
page "Known Problems" is designed as a quick overview of the system's
health. All problems related to the status of the nodes and their
interconnection are listed here. The "Start/Stop Daemon" page allows
start or stop all daemons and clients. The item "Log Files" opens a
window which shows the log files of all daemons and clients.


FS Operations
-------------

The menu item "FS Operations"->"Stripe Settings" allows you to view
and change the striping information in your file system. In BeeGFS, it
is possible to define the chunk-size of data that will be written, as
well as the number of storage targets, over which one file will
typically be distributed. The corresponding information can be
retrieved on this page. Furthermore, if you logged in with
administrative privileges, the system will allow you to change these
settings for each directory in the file system.

With the file browser you can browse through the global BeeGFS and
retrieve information on the stored files. Please note, that although
you are able to see directories and files, you will not be able to
view the content.


Installation
------------

The management pages contain elements for automation and
simplification of the installation/uninstallation tasks. Please refer
to the BeeGFS installation guide for a detailed description. Also the
installation log file is available in this menu.


Admon Menu Bar
==============

The menu bar contains options which are not required for the
installation and the day by day administration.


Admon
-----

The menu item "Change Settings" contains the configuration options of
the GUI. Also the logout option and the close option for the GUI.


Administration
--------------

The options inside the menu item "User Settings" allow you to change
the login passwords and to disable the password for the Information
user. The effect of the latter is that users can view the web-frontend
without being asked for a password. (The administrative account is not
affected by this setting).

The menu item "Mail Settings" lets you define some values for e-Mail
notifications by the software. If configured accordingly, an
administrator can receive an e-Mail whenever a node in the system
appears to be down.  These pages are only accessible by the user
"Administrator".
