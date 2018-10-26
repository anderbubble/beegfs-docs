.. from https://www.beegfs.io/docs/whitepapers/Introduction_to_BeeGFS_by_ThinkParQ.pdf

BeeGFS is a scale-out network file system based on POSIX, so
applications do not need to be modified.

BeeGFS spreads data across multiple servers to aggregate capacity and
performance of all servers.

BeeGFS is opensource and comes with optional Enterprise features like
high availability.

BeeGFS can be accessed from different operating systems and runs on
different platforms, including ARM and OpenPOWER.

BeeGFS file systems consist of 4 services: The management, storage,
metadata, and client service.

BeeGFS runs on top of local Linux file systems, such as ext4, xfs or
zfs.

The management service is only the “meeting point” for the other
services and not involved in actual file operations.

The metadata service is a scale-out service, meaning there can be an
arbitrary number of metadata services to increase performance.

Metadata is very small and typically stored on flash drives.

The storage service stores the user file contents.

The storage service typically uses RAID6 or RAIDz2 volumes as storage
targets.

Even a single large file is distributed across multiple storage
targets for high throughput.

Different target chooser algorithms are available to influence the
placement of files on storage targets.

The client is automatically built for the currently running Linux
kernel, so that kernel updates require no manual intervention.

Access to BeeGFS through NFS, CIFS or from Hadoop is also possible.

BeeGFS can easily reintegrate servers that were temporarily
unavailable.

Integrated Buddy Mirroring provides increased flexibility and
protection compared to classic shared storage approaches.

Buddy Mirroring takes advantage of the network’s fullduplex
capabilities for high throughput.

Storage Pools were designed to provide full all-flash performance in
systems with high capacity based on spinning disks.

Storage Pools are not limited to just two pools of flash drives and
spinning disks.

BeeOND elegantly unleashes the potential of the flash drives that you
already have in the compute nodes.

BeeOND can also be used when your global cluster file system is not
based on BeeGFS.

BeeGFS can be used on-premise or in the cloud.

BeeGFS can be tried out even with only a single machine for testing.

Questions? info@thinkparq.com
