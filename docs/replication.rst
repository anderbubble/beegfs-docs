======================================
Built-in Replication: Buddy Mirroring™
======================================

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

In normal operation, one of the storage targets (or metadata targets)
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


Metadata Service Buddy Mirroring
================================

As described in `Storage Service Buddy Mirroring`_ the same concept
and methodology can be used for metadata service buddy mirroring.
