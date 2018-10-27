======================
 BeeGFS APIs Overview
======================

Besides the POSIX interface, BeeGFS provides other APIs to take more
control of data placement, query additional information or access data
through other interfaces.

#. The `Striping API`_ allows the application developers to create
   files with individual stripe patterns, which are adjusted for the
   access pattern of a particular file. It also allows querying of
   stripe pattern details of files.

#. The `Cache API`_ provides functions to copy data between a fast
   BeeGFS cache file system (typically a BeeOND instance) and a global
   BeeGFS. Prefetching and flushing of data to and from the cache file
   system can be done synchronously and asynchronously. The
   asynchronous prefetch/flush offloads the copy task to a cache
   daemon, which copies the data with multiple threads to speed up the
   data transfer.

#. With the `Hadoop BeeGFS Connector`_, BeeGFS can be used as an
   alternative to the Hadoop file system (HDFS). Hadoop applications
   can use the general Hadoop file system API to access BeeGFS.

