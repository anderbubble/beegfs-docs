========================
BeeOND: BeeGFS On Demand
========================

Nowadays, compute nodes of a cluster typically are equipped with
internal flash drives to store the operating system and to provide a
local temporary data store for applications. But using a local
temporary data store is often inconvenient or not useful for
distributed applications at all, as they require shared access to the
data from different compute nodes and thus the high bandwidth and high
IOPS of the SSDs is wasted.

BeeOND (dubbed “beyond” and short for “BeeGFS On Demand”) was
developed to solve this problem by enabling the creation of a shared
parallel file system for compute jobs on such internal disks. The
BeeOND instances exist temporary exactly for the runtime of the
compute job exactly on the nodes that are allocated for the job. This
provides a fast, shared all-flash file system for the jobs as a very
elegant way of burst buffering or as the perfect place to store
temporary data. This can also be used to remove a lot of nasty I/O
accesses that would otherwise hit the spinning disks of your global
file system.

.. figure: beeond.png

   Figure 9: BeeOND: BeeGFS on demand

BeeOND is based on the normal BeeGFS services, meaning your compute
nodes will also run the BeeGFS storage and metadata services to
provide the shared access to their internal drives. As BeeOND does not
exclusively access the internal drives and instead only stores data in
a subdirectory of the internal drives, the internal drives are also
still available for direct local access by applications.

While it is recommended to use BeeOND in combination with a global
BeeGFS file system, it can be used independent of whether the global
shared cluster file system is based on BeeGFS or on any other
technology. BeeOND simply creates a new separate mount point for the
compute job. Any of the standard tools (like cp or rsync) can be used
to transfer data into and out of BeeOND, but the BeeOND package also
contains a parallel copy tool to transfer data between BeeOND
instances and another file system, such as your persistent global
BeeGFS.

BeeOND instances can be created and destroyed with just a single
simple command, which can easily be integrated into the prolog and
epilog script of the cluster batch system, such as Torque, Slurm or
Univa Grid Engine.
