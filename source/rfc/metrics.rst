Metrics
=======

Problem
-------

Zuul jobs are run as builds on independent, created on-demand, nodes. No information is available about the
state changes of the nodes throughout the whole buildset run in terms of CPU, memory, storage usage
or any other useful information related to the buildset itself for that matter. The only results coming
out of buildsets currently are test reports, run logs and built artifacts.

To have better understanding and control over the CI system and any insight on how changes in code
impact the building and testing processes, more information about the system the buildsets are being executed on
needs to be gathered and on the outputs of the builds themselves.

Metrics
-------

This section contains proposals of metrics to gather during and from buildsets.

#. General

   * run duration

#. Physical resources

   * CPU usage by time
   * memory usage by time
   * storage usage by time
   * disk IO by time
   * network IO by time

   *Note: in case of storage usage, might need to consider only specific mount points*

#. Test jobs specific metrics

   * number of tests run for a specific buildset - might be useful in enforcing that more tests are added
   * duration of each test case execution
   * run log size

#. Packaging jobs specific metrics

   * size of created packages

#. Container builder jobs specific metrics

   * size of created images

Technical requirements
----------------------

#.  Each buildsets metrics should be gathered separately, with consideration of review and patchset ID's. This
    would require for the metrics naming convention to be of the form:

      buildset.<pipeline>.<project-name>.<job-name>.<gerrit-change-id>.<gerrit-patchset>.<resource>

    for the check, gate and experimental pipelines. For the other pipelines a timestamp of the start of the jobs should be used
    instead of gerrit-change-id and gerrit-patchset:

      buildset.<pipeline>.<project-name>.<job-name>.<job-start-timestamp>.<resource>

    e.g.

      buildset.check.contrail-project-config.zuul-jobs-linters.42705.1.cpu
      buildset.check.contrail-project-config.zuul-jobs-linters.43333.3.mem

      buildset.periodic-sanity.contrail-analytics.contrail-systest-full-centos74-kolla-ocata.41414.2.notests
      buildset.periodic-sanity.contrail-analytics.contrail-systest-full-centos74-kolla-ocata.43343.4.logsize

      buildset.periodic-nightly.contrail-analytics.contrail-vnc-build-package-ubuntu-xenial.1527517025.packagesize

      buildset.periodic-nightly.contrail-analytics.contrail-vnc-build-containers-rhel7-ocata.1527522025.imagesize

#.  The metric gathering system (or the corresponding frontend) should allow to store the data (or transform and show)
    in a way that allows aligning the start timestamps of jobs to allow further processing of the data e.g. computing
    average values.

    Consider two test runs A and B (for a specific project) for which we gathered physical resources
    usage spread across 1000 points in time. A requirement would be to compute the average resource usage for the aligned
    data points, that is average CPU/mem etc usage in the 1, 2, 3 and so on second of the job run (assuming metrics
    are gathered each second).

*Note: metric gathering systems typically save metric values for either provided unix timestamps or for the moment the value was received.*
