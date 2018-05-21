External Package Managers
=========================

Purpose of this document
------------------------

This document contains design for implementation of handling external package managers and their mirroring in Contrail 5.0+ CI.

Problem
-------

Build system uses many different package managers during the package creation or container building phases. Those stages are prone to networking issues while accessing external package repositories. Currently, there are following package managers used:

* npm (NodeJS)
* maven (Java)
* pip (Python)

All of those are calling to the internet instead of using internal, lab-hosted mirrors.

Solution
--------

In general, we should be using one of two options:

1. Carefully curated set of external packages which are being mirrored hosted on internal mirrors.

   **Pros:**

   * Great control over packages being used in CI

   * Using the least amount of disk space.

   **Cons:**

   * Huge amount of work required to maintain the list of packages (almost impossible for NPM due to chain dependencies)

   * Hard to inject new packages during review phase.

   * Need to have some interface over the repository mirrors to add/remove packages.

   * Might require licensed software.

2. Using caching proxies for the repositories.

   **Pros:**

   * Easy to maintain.

   * Costless license-wise.

   **Cons:**

   * No control over the contents.

   * Might require big amounts of disk space.

   * Each packages has to be downloaded *at least once* - in case of bigger network issues it might be a problem.

Specifications of repositories used in CI
-----------------------------------------

Python Package Index (PyPI)
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Location: 

* https://pypi.org (registry)

* https://files.pythonhosted.org (package files)

Notes:

* requires usage of HTTPS by default

* creates calls to multiple hosts during download (search in registry and download from file server)

Node Package Manager (npm)
^^^^^^^^^^^^^^^^^^^^^^^^^^

Location: https://registry.npmjs.org

Notes:

* Registry and files hosting on the same host

Maven
^^^^^

* TODO

