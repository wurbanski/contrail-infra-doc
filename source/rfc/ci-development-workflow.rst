CI development workflow
=======================

This document describes the rules that control the development of source code responsible for the testing and build of OpenContrail.

Approval workflow for CI projects hosted on Gerrit
--------------------------------------------------

Each CI project has a corresponding user group that contains core reviewers:

- contrail-project-config-core
- contrail-zuul-jobs-core

Each change to those projects should go through a two-step review process, both 
steps have to be performed by reviewers other than the author of the change:

#. First core reviewer that accepts the change should place a ``Code-Review +2``
   vote
#. Second core reviewer that accepts the change should place an ``Approved +1``
   vote that will result in a merge (if the CI tests pass)

The author of the change (regardless if he's in the core reviewers group), can place
a ``Code-Review -2`` vote under his review to prevent others from reviewing it. This
vote should come with a comment explaining the reason behind it.

Approval workflow for CI projects hosted on GitHub
--------------------------------------------------

Juniper/contrail-infra
**********************

This repo contains Puppet code used to configure the Contrail CI services.

#. create your own fork of the repo
#. make sure that you perform a syntax check of your changes: https://github.com/Juniper/contrail-infra/blob/development/doc/source/contributing.rst#syntax-checks-and-linting
#. commit your local changes and push the commits to some branch of your fork (e.g. myfix1)
#. create a pull request from <your fork>:myfix1 to Juniper/contrail-infra:development
#. get approval from a user with permissions
#. merge your PR using the "Create merge commit" strategy
#. to push the changes further to the production branch, create an intermediate commit on the "staging" branch (origin is the J/c-i repo):

   #. ``git checkout production``
   #. ``git checkout -B staging``
   #. ``git merge origin/development``
   #. ``git push origin staging``
#. create a pull request from Juniper/contrail-infra:staging to Juniper/contrail-infra:production
#. get approval from a user with permissions
#. merge your PR using the "Create merge commit" strategy
#. after your PR gets merged, push the changes to the production Puppet server (ci-puppetmaster.opencontrail.org)

Juniper/zuul-jobs
**********************

This is a patched fork of the upstream Zuul job/role library. Custom patches to this repo
are submitted without established approval process.

Juniper/zuul
**********************

This is a patched fork of the Zuul source repository, changes can be submitted by
all the authorized GitHub users. Zuul updates are performed by rebasing the patches onto
upstream branch tips. Custom patches to Zuul are submitted without established approval
process.

Emergency procedures
--------------------

In emergency situations, when the change is required to fix an error affecting
majority of the CI jobs or daily build jobs, the author can merge the change on
his own by placing both `CR+2` and `A+1` votes under his change. This will still
require successfully passing the check and gate pipelines.
If the CI system is broken beyond all recognition and the jobs are failing even for the
change that is supposed to fix the system, the CI can be bypassed by placing
a `Verified+2` vote and manually submitting the change for merging. This can be done:
- via the Gerrit webui using an account with `Project-bootstrappers` membership
- via CLI, by logging in to the zuulv3.opencontrail.org and issuing Gerrit CLI commands.


Testing changes in contrail-zuul-jobs
-------------------------------------

The contrail-zuul-jobs repo contains roles and jobs for various projects, so
it is impractical to execute all the affected jobs in the check pipeline for
this repo. The only checks that are performed are unit tests and linting of
the Ansible code. If you're preparing a change that modifies a job for a
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

Because Zuul doesn't allow cycles in change dependencies (using the ``Depends-On:`` tags),
sometimes it is necessary to perform a transitional change in CI jobs. When one
wants to make a breaking change in one of the repos used in CI, he first
modifies the CI jobs to handle both versions of the repo (before and after
the breaking change), then merges the change in the repo, which now can pass CI
and then removes the support for the code before the change.
In case this is not possible, one can use the steps described in
"Emergency procedures" section.
