.. Copyright (C)  2014 Champs Libres Cooperative SCRLFS
   Permission is granted to copy, distribute and/or modify this document
   under the terms of the GNU Free Documentation License, Version 1.3
   or any later version published by the Free Software Foundation;
   with no Invariant Sections, no Front-Cover Texts, and no Back-Cover Texts.
   A copy of the license is included in the section entitled "GNU
   Free Documentation License".

Create a new bundle
********************

Create your own bundle is not a trivial task.

The easiest way to achieve this is seems to be : 

1. Prepare a fresh installation of the chill project, in a new directory
2. Create a new bundle in this project, in the src directory
3. Initialize a git repository **at the root bundle**, and create your initial commit.
4. Register the bundle with composer/packagist. If you do not plan to distribute your bundle with packagist, you may use a custom repository for achieve this [#f1]_
5. Move to a development installation, made as described in the :ref:`installation-for-development` section, and add your new repository to the composer.json file
6. Work as :ref:`usual <editing-code-and-commiting>`

.. warning::

    This part of the doc is not yet tested

TODO


.. rubric:: Footnotes

.. [#f1] Be aware that we use the Affero GPL Licence, which ensure that all users must have access to derivative works done with this software.
