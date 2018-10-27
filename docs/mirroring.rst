=====================================
Built-in Replication: Buddy Mirroring
=====================================

With BeeGFS being a high-performance file system, many BeeGFS users
try to optimize their systems for best performance for the given
budget. This is because with an underlying RAID-6, the risk of loosing
data on the servers is already very low. So the only remaining risk
for data availability is the relatively low risk of server hardware
issues like mainboard failures. But even in this case, data is not
lost, but only temporarily unavailable. Clients that need to access
the data of unavailable servers will wait for a configurable amount of
time (usually several minutes). If the server is available again
within this time, the client will continue transparently for the
application. This mechanism also enables transparent rolling
updates. If the server does not come back within this time frame, the
client will report an error to the application. This is useful to
prevent the system from hanging completely when a server is down.

However, there are of course systems where this small risk of data
unavailability is not acceptable.

The classic approach to this is hardware shared storage, where pairs
of two servers have shared access to the same external JBOD. While
this approach is possible with BeeGFS, it complicates the system
administration due to the shared disks and it requires extra services
like pacemaker to manage the failover. This approach also only moves
the availability problem from the servers to the shared JBOD and does
not reduce the risk of data loss if there is a problem with a RAID-6
volume.

Thus, BeeGFS comes with a different mechanism that is fully integrated
(meaning no extra services are required) and which does not rely on
special hardware like physically shared storage. This approach is
called Buddy Mirroring, based on the concept of pairs of servers (the
so-called buddies) that internally replicate each other and that help
each other in case one of them has a problem.

Compared to the classic shared storage approach, Buddy Mirroring has
the advantage that the two buddies of a group can be placed in
different hardware failure domains, such as different racks or even
different adjacent server rooms, so that your data even survives if
there is a fire in one of the server rooms. Buddy Mirroring also
increases the level of fault tolerance compared to the classic
approach of shared RAID-6 volumes, because even if the complete RAID-6
volume of one buddy is lost, the other buddy still has all the data.

For high flexibility, Buddy Mirroring can be enabled for all data or
only for subsets of the data, e.g. based on individual directory
settings, which are automatically derived by newly created
subdirectories. Buddy Mirroring can also be enabled anytime later
while data is already stored on the system.

Buddy Mirroring is synchronous (which is important for transparent
failovers), meaning the application receives the I/O completion
information after both buddies of a group received the data. The
replication happens over the normal network connection, so no extra
network connection is required to use Buddy Mirroring.

Buddy Mirroring can be enabled individually for the metadata service
and the storage service.


Storage Service Buddy Mirroring
===============================

A storage service buddy group is a pair of two targets that internally
manages data replication between each other. In typical setups with an
even number of servers and targets, the buddy group approach allows up
to a half of all servers in a system to fail with all data still being
accessible.

.. figure: storage-service-mirror.png

   Figure 6: Storage Service Buddy Mirror Groups

Storage buddy mirroring can also be used with an odd number of storage
servers. This works, because BeeGFS buddy groups are composed of
individual storage targets, independent of their assignment to
servers, as shown in the following example graphic with 3 servers and
2 storage targets per server. (In general, a storage buddy group could
even be composed of two targets that are attached to the same server.)

.. figure: storage-service-mirror-odd.png

   Figure 7: Storage Mirroring with odd Number of Servers

In normal operation, one of the storage targets (or metadata servers)
in a buddy group is labeled as primary, whereas the other is labeled
as secondary. These roles can switch dynamically when the primary
fails. To prevent the client from sending the data twice (and thus
reducing the maximum client network throughput), modifying operations
will always be sent to the primary by the client, which then takes
care of the internal data forwarding to the secondary, taking
advantage of the full-duplex capability of modern networks.

If the primary storage target of a buddy group is unreachable, it will
get marked as offline and a failover to the secondary will be
issued. In this case, the former secondary is going to be the new
primary. Such a failover is transparent and happens without any loss
of data for running applications. The failover will happen after a
short delay to guarantee the consistency of the system while the
change information is propagated to all nodes.

If a failed buddy comes back, it will automatically resynchronize with
the primary. To reduce the time for synchronization, the storage
buddies only synchronize files that have changed while the other
machine was down.

The beegfs-ctl command line tool can be used to configure the buddy
groups, enable or disable mirroring and to check the state of the
buddies.


Buddy Groups
============

Mirror buddy groups are numeric IDs, just like the numeric IDs of the
storage targets. Please note that buddy group IDs don't conflict with
target IDs, i.e. they don't need to be distinct from storage target
IDs.

There are basically two different ways to define buddy groups. They
can be defined manually or you can tell BeeGFS to create them
automatically.

Of course, defining groups manually gives you greater control and
allows you to create more detailed configuration. For example, the
automatic mode won't consider targets that are not equally sized. It
also doesn't know about the topology of your system, so if you, for
example, want to make sure that members of buddy groups are placed in
different physical locations you have to define them manually.


Define Buddy Groups automatically
---------------------------------

Automatic creation of buddy groups can be done beegfs-ctl, separately
for metadata and for storage servers::

  $ beegfs-ctl --addmirrorgroup --automatic --nodetype=meta

::

  $ beegfs-ctl --addmirrorgroup --automatic --nodetype=storage

Please see the help of beegfs-ctl for more information on available
parameters::

  $ beegfs-ctl --addmirrorgroup --help


Define Buddy Groups manually
----------------------------

Manual definition of mirror buddy groups can be useful if you want to
set custom group IDs or if you want to make sure that the buddies are
in different failure domains (e.g. different racks). Manual definition
of mirror buddy groups is done with the beegfs-ctl tool. By using the
following command, you can create a buddy group with the ID 100,
consisting of targets 1 and 2::

  $ beegfs-ctl --addmirrorgroup --nodetype=storage --primary=1 --secondary=2 --groupid=100

Please see the help of beegfs-ctl for more information on available
parameters::

  $ beegfs-ctl --addmirrorgroup --help

When creating mirror buddy groups for metadata manually and one of
them contains the root directory, it is necessary to set this one as
primary.


List defined Mirror Buddy Groups
--------------------------------

Configured mirror buddy groups can be listed with beegfs-ctl (don't
forget to specify the node type)::

  $ beegfs-ctl --listmirrorgroups --nodetype=storage

::

  $ beegfs-ctl --listmirrorgroups --nodetype=meta

It's also possible to list mirror buddy groups alongside other target
information::

  $ beegfs-ctl --listtargets --mirrorgroups

Please see the help of beegfs-ctl for more information on available
parameters::

  $ beegfs-ctl --listtargets --help


Metadata Service Buddy Mirroring
================================

As described in `Storage Service Buddy Mirroring`_ the same concept
and methodology can be used for metadata service buddy mirroring.

Note that odd numbers of storage this is not possible with metadata
servers, since there are no metadata targets in BeeGFS. An even number
of metadata server is needed so that every metadata server can belong
to a buddy group.


Define Stripe Pattern
=====================

After defining storage buddy mirror groups in your system, you have to
define a :doc:`data stripe <striping>` pattern that uses it.


Enabling and disabling Mirroring
================================

By default, mirroring is disabled for a new file system instance. Both
types of mirroring can be enabled with the beegfs-ctl command line
tool. (The beegfs-ctl tool is contained in the beegfs-utils package
and is usually run from a client node.)

Before metaadata or storage mirroring can be enabled, buddy groups
need to be defined, as these are the basis for mirroring.

Storage mirroring can be enabled on a per-directory basis, so that
some data in the file system can be mirrored while other data might
not be mirrored. On the medatada side, it is also possible to activate
or deactivate mirroring per directory, but certain logical
restrictions apply. For example, for a directory to be mirrored
effectively, the whole path to it must also be mirrored.

Mirroring settings of a directory will be applied to new file entries
and will be derived by new subdirectories. For instance, if metadata
mirroring is enabled for directory /mnt/beegfs/mydir1, then a new
subdirectory /mnt/beegfs/mydir1/mydir2 will also automatically have
metadata mirroring enabled.

After metadata mirroring is enabled for a file system using the
beegfs-ctl --mirrormd command, the metadata of the root directory will
be mirrored by default. Therefore, newly created directories under the
root will also have metadata mirroring enabled. It is possible to
exclude new folders from metadata mirroring by creating them using
beegfs-ctl --createdir --nomirror.

To enable file contents mirroring for a certain directory, see the
built-in help of the beegfs-ctl tool.

::

   $ beegfs-ctl --setpattern --buddymirror --help

File contents mirroring can be disabled afterwards by using beegfs-ctl
mode --setpattern without the --buddymirror option. However, files
that were already created while mirroring was enabled will remain
mirrored.
   
To check the metadata and file contents mirroring settings of a
certain directory or file, use::

  $ beegfs-ctl --getentryinfo /mnt/beegfs/mydir/myfile

To check target states of storage targets, use::

  $ beegfs-ctl --listtargets --nodetype=storage --state


Activating Metadata Mirroring
-----------------------------

After metadata server buddy groups have been defined as described
under Management of Mirror Buddy Groups, metadata mirroring can be
activated on the file system.

.. note:: When applying the beegfs-ctl --mirrormd command, no clients
          may be mounted. This can be achieved by stopping the
          beegfs-client service beforehand, and starting it again
          after beegfs-ctl --mirrormd was successfully performed. In
          addition, please restart the beegfs-meta service on all
          nodes afterwards.

To active metadata mirroring, use the beegfs-ctl tool::

  $ beegfs-ctl --mirrormd

In order to synchronize all information across the various components
of BeeGFS correctly, the clients can not be mounted during this
process, and the metadata servers must be restarted afterwards.

The full series of steps in an already running system is: stop all
clients, execute beegfs-ctl --mirrormd, restart all metadata servers,
then restart all clients.

Please see the help of beegfs-ctl for more information on available
parameters::

  $ beegfs-ctl --mirrormd --help

Running this command will enable metadata mirroring for the root
directory of the BeeGFS, as well as all the files contained in
it. Note that existing directories except for the root directory will
not be mirrored automatically. Please check `Migrating Existing
Metadata`_ to find out how to mirror existing directories.

New files and directories created inside a directory with active
metadata mirroring will also have metadata mirroring activated. There
might be situations where it is desirable to deactivate metadata
mirroring for part of the file systems. This gives a slight
performance boost, but the data will not be preserved in the event of
a server failure. A directory without metadata mirroring can be
created using beegfs-ctl::

  $ beegfs-ctl --createdir --nomirror <name>

For information about additional command line parameters, please see
the beegfs-ctl help::

  $ beegfs-ctl --createdir --help

Directories and files created inside a directory without metadata
mirroring will have metadata mirroring disabled.


Migrating Existing Metadata
---------------------------

If a file is moved into a directory with active metadata mirroring, it
will have its metadata mirrored. On the other hand, directories will
not automatically be mirrored when moved. For a directory to be
mirrored, it is therefore necessary to freshly create a directory
inside a mirrored directory. The easiest way to enable mirroring for a
whole directory tree is to do a recursive copy::

  $ cp -a <directory> <mirrored-dir>

This will also copy the file contents, therefore it is possible to use
it for enabling metadata and storage mirroring at the same time.


Restoring Metadata and Storage Target Data after Failures
=========================================================

If a storage target or metadata server is not reachable it will be
marked as offline and won't get data updates. Usually, when the target
or server re-registers it will automatically be synchronized from the
remaining mirror in the buddy group (self-healing). However, in some
cases it might be necessary that you manually start a synchronization
process.


Automatic Resynchronization
---------------------------

In general, if a secondary target or server is considered to be
out-of-sync, it is automatically set to the consistency state needs
resync (see "Target and Node States" for more information on states)
by the the management daemon.

The storage target resynchronization process for self-healing is
coordinated by the primary target. The standard process tries to avoid
unnecessary transfer of files. Therefore the primary target saves the
time of the last successful communication with the secondary
target. Only files which where modified after this timestamp will be
resynchronized by default. To avoid losing cached data, a short safety
threshold timespan will be added (defined by
sysResyncSafetyThresholdMins in beegfs-storage.conf).

Since metadata are much smaller than storage contents, there is no
timestamp-based mechanism in place, and instead the full mirrored
metadata of the metadata server will be sent to its buddy during the
resynchronization process.


Manual Resynchronization
------------------------

In some cases it might be useful or even neccessary to manually
trigger resynchronization of a storage target or metadata server. One
case, for example, is a storage system on the secondary target that is
damaged beyond repair. In this scenario all data of that target might
be lost and a new target needs to be brought up with the old
target ID. The automatic resync won't be sufficient then, because it
would only consider files after the last successful communication of
the targets. Another case for a manual resync override is when a fsck
of the underlying local file system (e.g. xfs_repair) has removed old
files.

The beegfs-ctl tool can be used to manually set a storage target or
metadata server to the needs resync state. Please note that this does
not trigger a resync immediately, but does only inform the management
daemon about the new state. The resync process then will be started by
the primary of that buddy group a few moments later.

As said before, the primary target saves the time of the last
successful communication with the secondary target. Without additional
parameters, this timestamp will be used to shorten resynchronization
times as much as possible. But it is also possible to override this
timestamp to resynchronize a longer timespan or to resynchronize
everything in the case described previously.

Please see the help of beegfs-ctl for more information on available
parameters::

  $ beegfs-ctl --startresync --help

If a resynchronization is already running and you want to abort it and
start anew, you can do so by passing the --restart parameter to
beegfs-ctl. If you don't, the current process keeps running and your
request will be ignored. This is particularly useful if the system
started an automatic resynchronization after a secondary target became
reachable again, but you know that the timestamp-based approach is not
sufficient. For example, this might be the case if your complete
underlying filesystem broke before the secondary target was started,
i.e. the target is completely empty and needs a full
synchronization. Note that restarting a running resync is only
possible for storage targets because metadata servers never do a
partial resynchronization.

The following command could be used to stop the automatic
resynchronization and start a full resynchronization instead.

::

   $ beegfs-ctl --startresync --nodetype=storage --targetid=X --timestamp=0 --restart


Display Resynchronization Information
-------------------------------------

The beegfs-ctl command line tool can be used to display information on
an ongoing resynchronization process.

Please see the built-in help of beegfs-ctl for more information on
available parameters::

  $ beegfs-ctl --resyncstats --help


Caveats of Storage Mirroring
============================

Storage buddy mirroring provides protection against many failure modes
of a distributed system, such as drives failing, servers failing,
networks being unstable or failing, and a number of other modes. It
does not provide perfect protection if a system is degraded, mostly
only for the degraded part of the system. If any storage buddy group
is in degraded state, another failure may cause data
loss. Administrative actions can also cause data loss or corruption if
the system is in an unstable or degraded state. These actions should
be avoided if at all possible, for example by ensuring that no access
to the system is possible while the actions are performed.


Setting states of active storage targets
----------------------------------------

When manually changing the state of a storage target from GOOD to
NEEDS_RESYNC, clients accessing files during a period of propagation
"see" different versions of the global state. This influences data and
file locks. Propagation happens every 30 seconds, so the period will
not take longer than a minute. This may happen because the state is
not synchronously propagated to all clients, which makes the following
sequence of events possible:

#. An administrator sets the state of an active storage target which
   is the secondary of a buddy group to NEEDS_RESYNC with beegfs-ctl
   --startresync.

#. The state is propagated to the primary of the buddy group. The
   primary will no longer forward written data to the secondary.

#. A client writes data to a file residing on the buddy group. The
   data is not forwarded to the secondary.

#. A different client reads data from the file. If the client attempts
   to read from the primary, no data loss occurs. If the client
   attempts to read from the secondary, which is possible without
   problems in a stable system, the client will receive stale data.

If the two clients in this example used the file system to
communicate, eg by calling flock for the file they share, the second
client will not see the expected data. Accesses to the file will only
stop considering the secondary as a source once all clients have
received the updated state information, which may take up to 30
seconds.

Setting the state of a primary storage target may exhibit the same
effects. Setting states for targets that are currently GOOD, and by
that triggering a switchover, must be avoided while clients are still
able to access data on the target. Propagation of the switchover takes
some time during which clients may attempt to access data on the
target that was set to non-GOOD. If the access was a write, that write
may be lost.


Fsync may fail without setting targets to NEEDS_RESYNC
------------------------------------------------------

When fsync is configured to propagate to the storage servers and
trigger an fsync on the storage servers, an error during fsync may
leave the system in an unpredictable state if the error occurred on
the secondary of a buddy group. If the fsync operation failed on the
secondary due to a disk error the error may be detected only during
the next operation of the secondary. If a failover happens before the
error is detected the automatic resync from the new primary (old
secondary, which has failed) to the new secondary (old primary) may
cause data loss.
