=============
Storage Pools
=============

While all-flash systems usually are still too expensive for systems
that require large capacity, a certain amount of flash drives is
typically affordable in addition to the spinning disks for high
capacity. The goal of a high-performance storage system should then be
to take optimal advantage of the flash drives to provide optimum
access speed for the projects on which the users are currently
working.

One way to take advantage of flash drives in combination with spinning
disks is to use the flash drives as a transparent cache. Hardware RAID
controllers typically allow adding SSDs as transparent block level
cache and also zfs supports a similar functionality through the L2ARC
and ZIL. While this approach is also possible with BeeGFS, the
effectiveness of the flash drives in this case is rather limited, as
the transparent cache never knows which files will be accessed next by
the user and how long the user will be working with those files. Thus,
most of the time the applications will still be bound by the access to
the spinning disks.

To enable users to get the full all-flash performance for the projects
on which they are currently working, the BeeGFS storage pools feature
makes the flash drives explicitly available to the users. This way,
users can request from BeeGFS (through the beegfs-ctl command line
tool) to move the current project to the flash drives and thus all
access to the project files will be served directly and exclusively
from the flash drives without any access to the spinning disks until
the user decides to move the project back to the spinning disks.

The placement of the data is fully transparent to applications. Data
stays inside the same directory when it is moved to a different pool
and files can be accessed directly without any implicit movement, no
matter which pool the data is currently assigned to. To prevent users
from putting all their data on the flash pool, different quota levels
exist for the different pools, based on which a sysadmin could also
implement a time-limited reservation mechanism for the flash pool.

.. figure: storage-pools.png

   Storage Pools

However, while the concept of storage pools was originally developed
to take optimum advantage of flash drives in a system with high
capacity based on spinning disks, the concept is not limited to this
particular use-case. The sysadmin can group arbitrary storage targets
in such pools, no matter which storage media they use and there can
also be more than just two pools.

A storage pool simply consists of one or multiple storage targets. If
storage buddy mirroring is enabled, both targets of a mirror buddy
group must be in the same pool, and the mirror buddy group itself must
also be in the same storage pool.
