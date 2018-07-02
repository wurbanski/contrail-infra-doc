Nightly Builds
==============

Every 24 hours (at 7am UTC) we run a full OpenContrail build and publish the containers to `DockerHub <https://hub.docker.com/u/opencontrailnightly/>`_.

Tags
----
For every release we tag the containers in a following way:
``$OpenStackSKU-$Branch-$BuildNumber`` where ``$OpenStackSKU`` is OpenStack's major version (e.g. ocata), ``$Branch`` is always 'master' and ``$BuildNumber`` is a number of our build of the nightly release.

Logs
----
Logs for every periodic build are `here <http://logs.opencontrail.org/periodic-nightly/>`_ and are created for every nightly build.

Debug container
---------------

We include container ``contrail-debug`` in every release which contains debug symbols of OpenContrail binaries and ``manifest.xml`` file with commit id of every repository used in the build process. 
