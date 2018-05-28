CI development workflow
=======================

CI projects
-----------

The contrail-project-config repo (hereafter referred to as "c-p-c") contains main Zuul configuration and job definitions that are shared among many projects. Here you'll find:
- list of projects that can be verified in Zuul
- for the core projects: which CI jobs are executed by Zuul
- Zuul workflow definition: what are the 
- ansible code for performing tasks that require secrets: uploading packages to a repo server, uploading container images to DockerHub etc.

As the contrail-project-config jobs can use secrets, reviews for this
project will get effective only after a merge. In Zuul this is called a
"config" or "trusted" project. When you submit a review for c-p-c that
changes a job, the change will not be used during execution of the tests for
this review, as it happens for other projects.


The contrail-zuul-jobs repo contains other jobs and roles that do not
require access to secrets and are safe to test during the review process.
This makes them easier to develop, as one can test the changes before merging


Approval workflow for CI projects
---------------------------------

Each CI project has a corresponding user group that contains core reviewers 
- contrail-project-config-core
- contrail-zuul-jobs-core

Each change to those projects should go through a two-step review process:

#. First core reviewer that accepts the change should place a ``Code-Review +2``
   vote
#. Second core reviewer that accepts the change should place an ``Approved +1``
   vote that will result in a merge (if the CI tests pass)

Emergency procedures
--------------------

TODO (force-merging, fixing errors in c-p-c)


Testing changes in contrail-zuul-jobs
-------------------------------------

The contrail-zuul-jobs repo contains roles and jobs for various projects, so
it is impractical to execute all the affected jobs in the check pipeline for
this repo. The only checks that are performed are unit tests and linting of
the Ansible code. If you're preparing an change that modifies a job for a
project you should test it as a dependent change:

#. create your change for the c-z-j project (change A).
#. create a dummy change (e.g. add an empty file) for the related project
   (change B). In the commit message for change B, specify change A as a
   dependent change using the "Depends-On:" directive.
#. place a comment under change A that contains a reference to change B, so
   that reviewers can verify that the changed job passed.
#. make sure that checks for the change B pass.

Transitional changes in CI
--------------------------

TODO (discuss problems with c-p-c and c-z-j in different gate queues -
dependencies between other projects are impossible)
