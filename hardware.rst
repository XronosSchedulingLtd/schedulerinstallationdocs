Hardware requirements
=====================

Xronos Scheduler has pretty modest hardware requirements by modern standards.
As always, more RAM is better, and the use of an SSD instead of a
mechanical hard disk drive will significantly improve database response times.

Minimum
-------
- 2G RAM
- 10G Hard disk
- Reasonably modern CPU (e.g. 1.8GHz) with at least 2 cores


Recommended
-----------
- 4G RAM
- 20G SSD
- CPU with multiple cores


More RAM is always nice, but beyond 4G there is little more that Scheduler
can do with it.  On a long running system with 8G of RAM in a large school
the actual usage barely rises above 4G until the system is backed up.
Once the whole database, application and files have been cached in RAM,
the usage gets to about 4G, and then no more can be used.

Multiple CPU cores can do a lot to improve response times when many people
are using Scheduler at once.

Scheduler will actually run very acceptably on a Raspberry Pi 4 (4G version)
provided you add a USB3 SSD as the system drive.
