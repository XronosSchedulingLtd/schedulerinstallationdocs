***********************
Maintenance (cron) jobs
***********************

There are various tasks which you can run in the background at regular
intervals to update or maintain your Scheduler system.

These make use of the standard cron facility of UNIX-like systems.
A sample crontab is provided in the support directory of your scheduler
installation.  

Configuration prerequisites
===========================

When cron jobs run on your system they run as your selected user
but they aren't automatically given quite the same run-time environment
as an interactive user session.  For this reason, a couple of extra
steps are needed to enable them to run.

At the end of page on
`setting up Scheduler <https://xronos.uk/install/scheduler.html#run-it>`_
you were told to create an rvm alias.  Make sure you did actually do
that because without it the cron jobs won't know what Ruby environment
to use.

Likewise, your cron jobs need to know where your Scheduler installation
is, and whether you're running it in development or production mode.
Make a directory called "etc" in your Scheduler user's home directory
and copy the file support/whichsystem into it.  Then edit that file
to match your system.  There are comments within it to tell you what
to change.

::

  $ mkdir ~/etc
  $ cp support/whichsystem ~/etc
  $ vi ~/etc/whichsystem

Create crontab
==============

A sample crontab is provided - support/crontab - and it can be installed
very simply by typing:

::

  $ crontab support/crontab

You will probably want to review it first to see that the times given
suit you, and you want all the jobs which it creates.  Any which you
don't want can be commented out.  If you've not used the recommended
user name or directory structure then you can do a global edit to
this file to change it to match your system.

If you're not familiar with cron then you can type "man cron" to see
its documentation, but the lines are fairly easy to understand.
Here's a sample:

::

  # m h   dom mon dow   command
  0 3 * * 0-5 /home/scheduler/Work/Coding/scheduler/utils/checkclashes

The first line is a comment, with a mnemonic for remembering the
order of items on the next line:

* Minute - 0-59
* Hour - 0-23
* Day of Month - 1-31
* Month - 1-12
* Day of week - 0-6  (0 is Sunday)
* Command - whatever you want to run

The sample above says to run the command at 03:00 on days 0-5 (Sun to Fri)
regardless of the date.

List of jobs
============

The following sections are best read with reference to the sample
crontab provided.

MIS import
----------

Scheduler can import automatically each night from your MIS.  Currently
supported are SchoolBase, iSAMS and WCBS/Pass.  This task is sufficiently
large to warrant its own separate documentation.


SOCS import
-----------

Likewise, if you use SOCS for your sports fixtures, Scheduler can import
them automatically.  Again, documented separately.

Clash checking
--------------

There are two cron jobs - checkclashes and checkclashesahead which
run each night to identify double bookings of pupils.  Typically these
occur where, for instance, a group of pupils are going on a trip or
are involved in an extra-curricular activity.  Provided the trip information
has been entered into Scheduler, the cron jobs will then identify
exactly which pupils will be missing which lessons, and annotate the
lessons accordingly.  A red blob appears on the lesson in the general
calendar view and the teacher can click on the lesson to discover
exactly who will be missing.

This can be very useful in lesson planning.  1 or 2 pupils missing out
of 28 probably needs no special adjustment, but if 22 of them are missing
there is little point in running a conventional lesson.

The jobs are quite long-winded, since they check every lesson for every
pupil in the school and thus are best run at night.  The checkclashes
job does the current and following weeks, whilst the checkclashesahead
one does the following 4 weeks.

Clash notification
------------------

Staff can opt to receive e-mail notifications of projected absences from
their lessons, either immediately they are identified, at the start of the
week, or daily (or any combination).

Immediate notification is handled by the clash checking jobs themselves.
(Although the jobs run every night, staff are notified only when the absence
is first calculated.)

Daily and weekly notifications are triggered by the dailyclashsummary and
weeklyclashsummary cron jobs.

Daily reports
-------------

The daily_report job generates notification e-mails on the following topics:

* Resource requests awaiting approval
* Loadings for pooled resources
* Forms waiting to be filled in
* Resource requests to be re-confirmed
* Events in need of staff (e.g. mini-bus drivers)

Invigilation notification
-------------------------

In a similar way to clash notifications, the system can send out reminders
to individuals who have been scheduled to do exam invigilation.  The person
allocating the invigilation slots can choose to send out notifications
at the time of allocation, but then the system will also send out
daily and weekly reminders if desired.  Again, each individual member of
staff can turn these on or off according to preference.  They default to on.


Daily maintenance
-----------------

This job runs each night to do some housekeeping tasks within the system.
Specifically:

* Adjust the currency of groups
* Check the consistency of user files
* Purge old e-mails from the journal

The first item above warrants a little extra explanation.  Each group
within the Scheduler system has a start date and potentially an end
date.  Between those dates it is "current", but once the end date
has passed it ceases to be current.  Typically this is used for things
like teaching groups or activity groups which come to an end at the
end of the academic year.  The cron job simply checks for any groups
which have passed their end dates and switches them off if they have.
