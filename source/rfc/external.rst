External Software Managers
==========================

Purpose of this document
------------------------

This document contains design for implementation of handling external software managers and their mirroring in Contrail 5.0+ CI.

Problem
-------

Build system uses many different package managers during the package creation or container building phases. Those stages are prone to networking issues while accessing external repositories. Currently, the following software managers are used:

* npm (NodeJS)
* maven (Java)
* pip (Python)
* docker

All of those are calling to the internet instead of using internal, lab-hosted mirrors.

1. Describe the means of mirroring external sources.
2. Describe the means of using them in CI.

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

   * Some package managers have difficult to reproduce protocols for acquiring packages.

Specifications of repositories used in CI
-----------------------------------------

Python Package Index (PyPI)
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Location: 

* https://pypi.org (registry)

* https://files.pythonhosted.org (package files)

Configuration required:

* Add option ``index-url`` to a config file (global config located at ``/etc/pip.conf``). Configure as ``trusted-host`` as well.

Notes:

* requires usage of HTTPS by default

* creates calls to multiple hosts during download (search in registry and download from file server)

Node Package Manager (npm)
^^^^^^^^^^^^^^^^^^^^^^^^^^

Location: https://registry.npmjs.org

Configuration required:

* Add option ``registry`` to a config (global: ``/etc/npmrc``)

Notes:

* Registry and files hosting on the same host

* Easy to cache using e.g. nginx as a cache server.

* There are custom built solutions for npm caching, e.g. local-npm or commercial products like Sonatype Nexus or JFrog Artifactory.

Maven
^^^^^

Location:

* https://search.maven.org

Configuration required:

* Configure mirror in settings.xml file as per `documentation <https://maven.apache.org/guides/mini/guide-multiple-repositories.html>`_.

Notes:

* Usual way of caching maven is by using tools designed for that, e.g. SonaType Nexus or JFrog Artifactory.

* It might be posibble to use pass-through cache for maven.

Docker
^^^^^^

Location: https://hub.docker.com/

Configuration required:

* Add ``"registry-mirrors": ['http://<docker-mirror-host>:<port>']`` to ``/etc/docker/daemon.json`` configuration object.

Notes:

* Docker registry (https://hub.docker.com/_/registry) can work in pull-through cache mode, docker daemons have to be configured for the internal repository to be used. 

  This registry can proxy traffic to multiple other registries, even requiring login to use. It also *untags up old images automatically* when new ones are pulled for the same tags. Garbage collection has to be run separately.

  Documentation can be found `here <https://docs.docker.com/registry/recipes/mirror/>`_.
