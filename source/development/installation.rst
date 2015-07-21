.. Copyright (C)  2014 Champs Libres Cooperative SCRLFS
   Permission is granted to copy, distribute and/or modify this document
   under the terms of the GNU Free Documentation License, Version 1.3
   or any later version published by the Free Software Foundation;
   with no Invariant Sections, no Front-Cover Texts, and no Back-Cover Texts.
   A copy of the license is included in the section entitled "GNU
   Free Documentation License".

.. _installation-for-development :

Installation for development
****************************

Installation for development should let you able to have an access to the source code, upload the code to our CVS (i.e. `git`_), and working with `composer`_.

As Chill is divided into bundles (the Symfony name for 'modules'), each bundle has his own repository.

Installation and big picture
----------------------------

At first, you should install Chill as described in the :ref:`basic-installation` section.

Two things must be modified : 

At first, You should add the `--prefer-source` argument when you create project. 

.. code-block:: bash

   composer create-project chill-project/standard path/to/your/directory --stability=dev --prefer-source

Secondly, if composer ask you the following question : ::

  Do you want to remove the existing VCS (.git, .svn..) history? [Y,n]?

**You should answer `n` (no).**

This will install a project. All downloaded bundles will be stored in the `/vendor` directories. In those directories, you will have access to the git commands.

.. code-block:: bash

    $ cd vendor/chill-project/main/
    $ git remote -v
    composer   git://github.com/Chill-project/Standard.git (fetch)
    composer   git://github.com/Chill-project/Standard.git (push)
    origin     git://github.com/Chill-project/Standard.git (fetch)
    origin     git@github.com:Chill-project/Standard.git (push)

Files cleaning after installation
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Composer will delete unrequired files, and add some. This is perfectly normal and will appears in your git index.
But you should NOT delete those files.

This is the expected 'git status' result: 

.. code-block:: bash

    $git status
      #(...)

      Modifications qui ne seront pas validées :
        (utilisez "git add/rm <fichier>..." pour mettre à jour ce qui sera validé)
        (utilisez "git checkout -- <fichier>..." pour annuler les modifications dans la copie de travail)

	      modifié:         app/SymfonyRequirements.php
	      supprimé:        app/SymfonyStandard/Composer.php
	      supprimé:        app/SymfonyStandard/RootPackageInstallSubscriber.php
	      modifié:         app/check.php

You can ignore the local changes using the `git update-index --assume-unchanged` command.

.. code-block:: bash

   $ git update-index --assume-unchanged app/check.php
   $ git update-index --assume-unchanged app/SymfonyRequirements.php
   $ git update-index --assume-unchanged app/SymfonyStandard/Composer.php
   $ git update-index --assume-unchanged app/SymfonyStandard/RootPackageInstallSuscriber.php

Working with your own fork
^^^^^^^^^^^^^^^^^^^^^^^^^^

Ideally, you will work on a fork of the main github repository. 
To ensure that composer will download the code from **your** repository, you will have to adapt the `composer.json` file accordingly, using your own repository. 

Add the following lines to your composer.json file if you want to force composer to download from your own repository: 

.. code-block:: json

    "repositories": [
       {
         "type": "git",
         "url": "git://github.com/your-github-username/ChillMain.git"
       }
    ]

You may also `use aliases <https://getcomposer.org/doc/articles/aliases.md>`_ to define versions.

.. _editing-code-and-commiting :

Editing the code and commiting
------------------------------

You may edit code in the `vendor/path/to/the/bundle` directory.

Once satisfied with your changes, you should commit as usually : 

.. code-block:: bash

    $ cd vendor/path/to/bundle
    $ git status 
    Sur la branche master
    Votre branche est à jour avec 'origin/master'.

    rien à valider, la copie de travail est propre

.. warning

    The git command must be run from you vendor bundle's path (`vendor/path/to/bundle`). 

Tips
^^^^

The command `composer status` (`see composer documentation <https://getcomposer.org/doc/03-cli.md#status>`_) will give you and idea of which bundle has been edited :

.. code-block:: bash

    $ cd ./../../ #back to the root project directory
    $ composer status
    You have changes in the following dependencies:
    /path/to/your/project/install/vendor/chill-project/main
    Use --verbose (-v) to see modified files




.. _git: http://git-scm.org
.. _composer: https://getcomposer.org
