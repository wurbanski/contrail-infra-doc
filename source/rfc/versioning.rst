Upstream Components Versioning 
============================== 

Purpose of this document       
------------------------       
  
This document contains design for implementation of external/upstream components usage and mirroring in Contrail 5.0+ project.
  
Problem
-------

Upstream versions of operating systems and other software (e.g. OpenStack) change in schedules different than that of Contrail's. Red Hat family of operating systems replaces a previous version with new one and drops the support for older ones. There are multiple combinations of supported OpenStack and CentOS/Red Hat releases and they all have to be tracked.
Using official repositories makes CI setup prone to network failures.
    
Solution
--------

Life cycle for external/upstream components has to be tracked in order to accomodate for changes in operating systems' and other software's versioning.
Additionally, any packages or source code that is used in CI should be mirrored for use in air-gapped environment to reduce dependencies on external sources and proneness to network failures.

Tracking release timelines
-----------------------------

Operating systems
^^^^^^^^^^^^^^^^^

1. **Red Hat Enterprise Linux**

   * `Red Hat Enterprise Linux Life Cycle <https://access.redhat.com/support/policy/updates/errata>`_
   * `RHEL7 Patch Release Dates <https://access.redhat.com/articles/3078#RHEL7>`_

2. **CentOS**

   *Note: CentOS Release schedule follows that of Red Hat's closely.*

   * `About CentOS <https://wiki.centos.org/About/Product>`_

3. **Ubuntu**

   * `About Ubuntu 16.04 <http://releases.ubuntu.com/16.04/>`_

OpenStack
^^^^^^^^^

All information about OpenStack releases can be found on `OpenStack Releases website <https://releases.openstack.org>`_.

Red Hat OpenStack Platform
^^^^^^^^^^^^^^^^^^^^^^^^^^

All information can be found on `Red Hat's Website <https://access.redhat.com/support/policy/updates/openstack/platform>`_.

Handling of upcoming versions
-----------------------------

Currently, Red Hat/CentOS are using a model where only the newest patch (minor) version is fully supported and support for the olders ones is immediately dropped unless a special license is provided (RHEL EUS). This can lead to unexpected results when the upgrade happens and latest repositories immediately start poining to newer patch-release.

There are two possible kinds of issues here:

1. Unexpected upgrade
   If the timelines are not tracked correctly and build system uses general repositories from the Internet, builds MAY start failing unexpectedly, leading to immediate and sudden work stops.

2. Lack of support for newer versions
   If the products are not tested with upcoming releases ahead of time, we won't know if the update breaks things. 

Operating systems
^^^^^^^^^^^^^^^^^

CentOS and RHEL allow using a *beta* channel for testing with pre-release software (release candidate). 

OpenStack
^^^^^^^^^

OpenStack repositories might be provided ahead of time in case of CentOS if there is a `SIG <https://wiki.centos.org/SpecialInterestGroup>`_ for that release.

Red Hat Openstack Platform
^^^^^^^^^^^^^^^^^^^^^^^^^^

Red Hat provides limited time beta testing programs before releasing a RHOSP version, those have to be tracked separately.

Mirroring required external resources
-------------------------------------

Technical means
---------------


