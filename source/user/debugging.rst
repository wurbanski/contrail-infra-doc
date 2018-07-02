Debugging
=========

Change not merging
------------------

Sometimes Zuul will refuse to merge a change even though all required votes are present. Reasons may include merge conflict or dependency issues.

Dependencies
````````````

There are two types of review dependencies: implicit and explicit.

**Implicit dependency** is a dependency introduced by the git tree structure - if ``review A`` is based on a parent commit from ``review B`` and ``review B`` is not merged yet, ``review B`` automatically becomes a dependency of ``review A``.

**Explicit dependency** is a dependency specified in git commit message by ``Depends-On: $Change-ID``.

Unittest failures
-----------------

Logs
````
You should probably start your debugging with checking what tests failed - you can do this in ``scons_test-FAIL.log.gz``. You can search for the tests and their results in ``job-output.txt.gz`` using the test name. 

If this doesn't help you, try searching for ``scons: *`` to see all errors raised by SCons - most often failures will be preceded by Python stacktrace.


Flakiness
`````````
Sometimes it's just test flakiness and there's not much you can do. To run the tests again, type ``recheck`` in a comment in your review.

If you suspect or have experienced some test flakiness, please let us know on #ci channel on Slack and/or contact test author.
