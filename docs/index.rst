===============================
BeeGFS Unofficial Documentation
===============================

BeeGFS is the leading parallel cluster file system. It has been
developed with a strong focus on maximum performance and scalability,
a high level of flexibility and designed for robustness and ease of
use.

BeeGFS is a software-defined storage based on the POSIX file system
interface, which means applications do not have to be rewritten or
modified to take advantage of BeeGFS. BeeGFS clients accessing the
data inside the file system, communicate with the storage servers via
network, via any TCP/IP based connection or via RDMA-capable networks
like InfiniBand (IB), Omni-Path (OPA) and RDMA over Converged Ethernet
(RoCE). This is similar for the communication between the BeeGFS
servers.

Furthermore, BeeGFS is a parallel file system. By transparently
spreading user data across multiple servers and increasing the number
of servers and disks in the system, the capacity and performance of
all disks and all servers is aggregated in a single namespace. That
way the file system performance and capacity can easily be scaled to
the level which is required for the specific use case, also later
while the system is in production.

BeeGFS is separating metadata from user file chunks on the
servers. The file chunks are provided by the storage service and
contain the data, which users want to store (i.e. the user file
contents), whereas the metadata is the “data about data”, such as
access permissions, file size and the information about how the user
file chunks are distributed across the storage servers. The moment a
client has got the metadata for a specific file or directory, it can
talk directly to the storage service to store or retrieve the file
chunks, so there is no further involvement of the metadata service in
read or write operations.

BeeGFS adresses everyone, who needs large and/or fast file
storage. While BeeGFS was originally developed for High Performance
Computing (HPC), it is used today in almost all areas of industry and
research, including but not limited to: Artificial Intelligence, Life
Sciences, Oil & Gas, Finance or Defense. The concept of seamless
scalability additionally allows users with a fast (but perhaps
irregular or unpredictable) growth to adapt easily to the situations
they are facing over time.

An important part of the philosophy behind BeeGFS is to reduce the
hurdles for its use as far as possible, so that the technology is
available to as many people as possible for their work. In the
following paragraphs we will explain, how this is achieved.

BeeGFS is open-source and the basic BeeGFS file system software is
available free of charge for end users. Thus, whoever wants to try or
use BeeGFS can download it from www.beegfs.io. The client is published
under the GPLv2, the server components are published under the BeeGFS
EULA.

The additional Enterprise Features (high-availability, quota
enforcement, and Access Control Lists) are also included for testing
and can be enabled for production by establishing a support contract
with ThinkParQ. Professional support contracts with ThinkParQ ensure,
that you get help when you need it for your production
environment. They also provide the financial basis for the continuous
development of new features, as well as the optimization of BeeGFS for
new hardware generations and new operating system releases. To provide
high quality support around the globe, ThinkParQ cooperates with
international solution partners.

System integrators offering turn-key solutions based on BeeGFS are
always required to establish support contracts with ThinkParQ for
their customers to ensure, that help is always available when needed.

Originally, BeeGFS was developed for Linux and all services, except
for the client are normal userspace processes. BeeGFS supports a wide
range of Linux distributions such as RHEL/Fedora, SLES/OpenSuse or
Debian/Ubuntu as well as a wide range of Linux kernels from ancient
2.6.18 up to the latest vanilla kernels.  Additionally, a native
BeeGFS Windows client is currently under development to enable
seamless and fast data access in a shared environment.

Another important aspect of BeeGFS is the support for different
hardware platforms, including not only Intel/AMD x86_64, but also ARM,
OpenPOWER and others. As the BeeGFS network protocol is independent of
the hardware platform, hosts of different platforms can be mixed
within the same file system instance, ensuring that sysadmins can
always add systems of a new platform later throughout the life cycle
of the system.

.. toctree::
   :maxdepth: 2
   :caption: Architecture

   architecture
   admon
   mirroring
   pools
   cloud
   striping
   client-tuning


.. toctree::
   :maxdepth: 2
   :caption: Deployment

   getting-started
   beeond


.. toctree::
   :maxdepth: 2
   :caption: Support

   contact
   license
