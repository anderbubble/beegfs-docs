=================
Cloud Integration
=================

BeeGFS is available on the Amazon Web Services (AWS) as well as on
Microsoft Azure.

For the Amazon Web Services integration, it comes in two flavors: The
community support edition is completely free of software charges,
while the professional support edition adds a monthly fee. The BeeGFS
instances can be launched directly from AWS Marketplace, where you can
also find details on pricing model.

Launching BeeGFS on AWS will result in a fully working BeeGFS setup,
which is readily available to store your large data sets for high
performance access.

By default, the BeeGFS instances on AWS use Elastic Block Storage
(EBS) volumes to preserve your data even when all virtual machines are
shut down, so that you can come back later to resume working with your
data sets and don’t need to keep your virtual machines running while
you are not using them.

The Microsoft Azure implementation provides Resource Manager templates
that will help deploying a BeeGFS instance based on CentOS 7.2.

Of course, if you are moving your datacenter into the cloud, it is
also possible to perform your own BeeGFS installation for full
flexibility instead of using the predefined templates and to get a
normal BeeGFS professional support contract like on-premise setups.
