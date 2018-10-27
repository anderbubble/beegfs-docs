========
Striping
========

Striping in BeeGFS can be configured on a per-directory and per-file
basis. Each directory has a specific stripe pattern configuration,
which will be derived to new subdirectories and applied to any file
created inside a directory. There are currently two basic parameters
that can be configured for stripe patterns: the desired number of
storage targets for each file and the chunk size (or block size) for
each file stripe.

The stripe pattern parameters of BeeGFS can be configured with the
Admon GUI or the command-line control tool. The command-line tool
allows you to view or change the stripe pattern details of each file
or directory in the file system at runtime.

The following command will show you the current stripe settings of
your BeeGFS mount root directory (in this case "/mnt/beegfs")::

  $ beegfs-ctl --getentryinfo /mnt/beegfs

Use mode setpattern to apply new striping settings to a directory (in
this case "stripe files across 4 storage targets with a chunksize of 1
MB")::

  $ beegfs-ctl --setpattern --numtargets=4 --chunksize=1m /mnt/beegfs

Stripe settings will be applied to new files, not to existing files in
the directory. With time, as files are continuously overwritten,
moved, copied, removed, and recreated, the new stripe pattern will
gradually be applied to all files in the directory.


Buddy Mirroring
===============

If you have buddy mirror groups defined in your system, you can set
the stripe pattern to use buddy groups as stripe targets, instead of
individual storage targets. In order to do that, add the
option --buddymirror to the command, as follows. In this particular
example, the data will be striped across 4 buddy groups with a chunk
size of 1 MB.

::

   $ beegfs-ctl --setpattern --numtargets=4 --chunksize=1m --buddymirror /mnt/beegfs

In BeeGFS version 7, this option has been replaced
with --pattern=buddymirror.


Impact on network communication
===============================

The data chunk size has an impact on the communication between client
and storage servers in several ways, as follows.

- When a process writes data on a file located on BeeGFS, the client
  identifies the storage targets that contain the data chunks that
  will be modified (by querying the metadata servers) and send
  modification messages to the storage servers containing the modified
  data. The maximum size of such messages is determined by the data
  chunk size of the file.

  If you define chunksize=1m, 1 MB will be the maximum size of each
  message. If the amount of data written to the file is larger than
  the maximum message size, more messages will have to be sent to the
  servers and this may cause performance loss. So, slightly increasing
  the chunk size to a few MB has the effect of reducing the amount of
  messages and this can have a positive performance impact, even in a
  system with a single target.

- In addition, it is important to make sure that a :doc:`data chunk
  fits the RDMA buffers <client-tuning>` available on the client, in
  order to prevent the messages from being split, in order to be
  transmitted over RDMA.

- You also have to consider the file cache settings. When the client
  is using the buffered cache (tuneFileCacheType = buffered), it uses
  a file cache buffer of 512 KB to accumulate changes on the same
  data. This data is sent to the servers only when data from outside
  the boundaries of that buffer is needed by the client. So, the
  larger this buffer, the less communication will be needed between
  the client and the servers. You should set this buffer size to a
  multiple of the data chunk size. For example, adding
  tuneFileCacheBufSize = 2097152 to the BeeGFS client configuration
  file will raise the file cache buffer size to 2 MB.
