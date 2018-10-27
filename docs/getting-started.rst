==========================================
Getting started and typical Configurations
==========================================

To get started with BeeGFS, all you need is a Linux machine to
download the packages from www.beegfs.io and to install them on. If
you want to, you can start with only a single machine and a single
drive to do some initial evaluation. In this case, the single drive
would be your management, metadata and storage target at the same
time. The :doc:`quickstart walk-through guide <quickstart>` will show
you all the necessary steps.

Typically, the smallest reasonable production system as dedicated
storage is a single machine with two SSDs in RAID1 to store the
metadata and a couple of spinning disks (or SSDs) in a hardware RAID6
or software zfs RAID-z2 to store file contents. A fast network (such
as 10GbE, OmniPath or InfiniBand) is beneficial but not strictly
necessary. With such a simple and basic setup, it will be possible to
scale in terms of capacity and/or performance by just adding disks or
more machines.

Often, BeeGFS configurations for a high number of clients in cluster
or enterprise environments consist of fat servers with 24 to 72 disks
per server in several RAID6 groups, usually 10 or 12 drives per RAID6
group. A network, usually InfiniBand, is used to provide high
throughput and low latency to the clients.

In smaller setups (and with fast network interconnects), the BeeGFS
metadata service can be running on the same machines as the BeeGFS
storage service. For large systems, the BeeGFS metadata service is
usually running on dedicated machines, which also allows independent
scaling of metadata capacity and performance.
