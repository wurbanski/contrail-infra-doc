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
