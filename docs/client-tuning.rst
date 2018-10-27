=============
Client Tuning
=============

Parallel Network Requests
=========================

- Each BeeGFS client establishes multiple network connections to the
  same server, which allows the client to have multiple network
  requests in flight to this server

  - The number of connections from a particular client to the same
    server can be configured by setting the value of
    connMaxInternodeNum in /etc/beegfs/beegfs-client.conf

- Increasing the number of connections may improve performance and
  responsiveness for certain workloads.

  - When increasing the value, it is extremely important keep the
    resulting RAM usage for network buffers on the servers in mind,
    especially for Infiniband and larger cluster setups. Make sure to
    read the comments for connMaxInternodeNum and connRDMABufSize in
    beegfs-client.conf to learn more about the server-side RAM usage.

  - On a compute node, it usually doesn't make sense to set this
    number higher than the number of CPU cores.

  - On a cluster login node, setting this value higher than the number
    of CPU cores may help to improve responsiveness when multiple
    users are active.

- BeeGFS clients establish connections only when they are needed (and
  drop them after some idle time). Use the command beegfs-net on a
  client to see the number of currently established connections to
  each of the servers.

  - beegfs-net is contained in the beegfs-utils package.

- The total space used by the buffers (connRDMABufSize x
  connRDMABufNum) should be larger or equal to the data chunk size, so
  that the messages exchanged between client and storage servers do
  not need to be split to fit into the buffers available. The default
  RDMA settings (connRDMABufSize = 64 KB, connRDMABufNum = 12) are OK
  for the default chunk size of 512 KB. If you set a chunk size of 1
  MB and a buffer size of 64 KB, the number of buffers should be at
  least 1 MB / 64 KB + 4 additional buffers for protocol, so 20 in
  this example.


Remote fsync
============

- BeeGFS clients have a configuration option to control behavior when
  a user application calls fsync()

  - The option is called tuneRemoteFSync in
    /etc/beegfs/beegfs-client.conf

  - The client can either enforce that data is committed to the server
    disks on fsync() (=> tuneRemoteFSync=true) or only make sure that
    data is transferred to the server-side cache (=>
    tuneRemoteFSync=false).

- Disabling remote fsync can significantly reduce disk seeks and thus
  improves performance for applications that use a lot of fsync()
  calls.


Disable locate/mlocate/updatedb
===============================

- Some Linux distributions install a locate tool by default, which
  scans all file systems once per day to build a database of existing
  files.

- In a cluster, you certainly would not want to have all of your
  compute nodes scan the entire BeeGFS file system each day.

- Either deactivate this service if you don't need it or edit the file
  /etc/updatedb.conf to make sure that the "beegfs" file system type
  is contained in the "PRUNEFS" list and you BeeGFS mountpoint is
  contained in the PRUNEPATHS list.
