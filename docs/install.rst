========================
 Installation and Setup
========================

There are two ways to install BeeGFS: GUI-based (using a graphical
Java interface) or manually (using shell commands).

The graphical installation is based on the BeeGFS Admon
("Administration and Monitoring") service, to which the graphical Java
interface connects. In general, the GUI-based installation is
recommended only for inexperienced users, because it does not provide
the full flexibility of a manual installation (e.g. several
configuration settings are not available and installation into an
image is not supported by the GUI-based installation procedure).

If you are familiar with the Linux command line, it is recommended to
perform the manual installation. Of course, you can still use the
graphical interface to view usage statistics after a manual
installation, if you want to.


General Notes
=============

Installation Paths

  BeeGFS binaries and libraries will be installed to /opt/beegfs. The
  configuration files are located in the directory /etc/beegfs. Each
  service (including the client) comes with an init script in
  /etc/init.d.

Log Files

  BeeGFS services create log files in /var/log.

Runtime Compatibility of different Versions

  Different versions of BeeGFS clients and servers are compatible if
  they are part of the same major release series (e.g. all v6.x
  versions are part of the same major release v6).
  
Storage Format Compatibility

  Some new major releases of BeeGFS may introduce new storage format
  features, which might require upgrades of on-disk data
  structures. Upgrade tools will be provided in these cases to convert
  existing data to the new format in place. See release changelog for
  compatibility notes.

Compiler

  The GNU C compiler (gcc) must be installed on client machines to
  build the BeeGFS client kernel modules.

Infiniband Libraries

  For native Infiniband support, you need to have the ibverbs and
  rdmacm libraries of OFED 1.2 or higher installed. (Most Linux
  distributions also ship with sufficiently recent versions of these
  libraries.)


GUI-based Installation and Service Management
=============================================

Graphical installation is performed through the BeeGFS Admon
("Administration and Monitoring"). It consists of a Java GUI and a
beegfs-admon service, to which the graphical interface connects. The
beegfs-admon service uses ssh to run installation commands on the
other BeeGFS nodes. Thus, passwordless ssh login from the host running
the beegfs-admon service to all BeeGFS nodes for user root is required
(including the node where the beegfs-admon service is running).

.. note:: If you don't know how to configure ssh without password, you
          might want to have a look at the tool ssh-keygen to create a
          public/private key pair (without a passphrase) and then use
          ssh-copy-id to copy the new key to the other nodes, or use
          your favorite search engine to search for "passwordless
          ssh".


Download and Installation of the Admon service
----------------------------------------------

Download the appropriate BeeGFS repository file for your Linux
distribution from the table below.

- Admon is the administration and monitoring service of BeeGFS.

- The Admon service typically runs on a cluster master node.

+------------------+-------+-------+------------------+
| Linux Base       |       |       |                  |
|Distribution      |Version|Package|                  |
|                  |       |Manager|Repository        |
|                  |       |       |File (Save        |
|                  |       |       |to...)            |
+==================+=======+=======+==================+
|       Red Hat    |       |       |                  |
|Linux (and        |       |       |                  |
|derivatives,      |   6.x |   yum |    Download (Save|
|e.g. Fedora)      |       |       |to:               |
|                  |       |       |/etc/yum.repos.d/)|
+                  +-------+       +------------------+
|                  |   7.x |       |                  |
+------------------+-------+-------+------------------+
|                  |       |       |                  |
+------------------+-------+-------+------------------+
|                  |       |       |                  |
+------------------+-------+-------+------------------+
|                  |       |       |                  |
+------------------+-------+-------+------------------+
