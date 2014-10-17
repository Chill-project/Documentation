.. Copyright (C)  2014 Champs Libres Cooperative SCRLFS
   Permission is granted to copy, distribute and/or modify this document
   under the terms of the GNU Free Documentation License, Version 1.3
   or any later version published by the Free Software Foundation;
   with no Invariant Sections, no Front-Cover Texts, and no Back-Cover Texts.
   A copy of the license is included in the section entitled "GNU
   Free Documentation License".

Installation
############


.. _basic-installation:

Basic installation
``````````````````



Chill is written in PHP and use the `symfony framework`_. We take advantages of all the framework's feature, and installation should be as simple as installing symfony.

Requirements
------------

Server requirements
^^^^^^^^^^^^^^^^^^^^

* a postgresql database. The minimum version is postgresql 9.3, but we are working on developing on the 9.4 branch, which will provide features which will ease developper work
* php 5.5
* If you run Chill in production mode, you should also install a web server (apache, ngnix, ...). We may use php built-in server for testing and development.

Within this documentation, we are going to describe installation on Unix systems (Unix, Mac OS). Windows installation ha not been tested yet.

You won't need any web server for demonstration or development.

Client requirements
^^^^^^^^^^^^^^^^^^^

Chill is accessible through a web browser. Currently, we focus our support on `Firefox`_, because firefox is open source, cross-platform, and very well active. The software should work with other browser (Chromium, Opera, ...) but some functionalities should break.

Preparation
-----------

You will need those informations :

* The informations to access to your database: host, port, database name, and your credentials (username and password) ;
* a random string, which will be use to improve entropy in security. Choose anything you want (random character, your father's birthplace, ...).

Installation
------------

Chill is installed with `composer`_.

Install composer
^^^^^^^^^^^^^^^^

..  note::
  If you do not know composer, it is a good idea to have a glance at `the composer documentation`_ 

Install composer on your system :

.. code-block:: bash

   curl -sS https://getcomposer.org/installer | php

move composer.phar to your system 
"""""""""""""""""""""""""""""""""

.. note::
   This part is not necessary, but this will be more convenient for the process. If you do not want to install composer on your system, you will have to replace the commands `composer` by `php composer.phar`.

Install composer globally on you system will made the installation process more convenient. To do this, simply run 

.. code-block:: bash

   sudo mv composer.phar /usr/local/bin/composer

Then, just run `composer` instead of `php composer.phar`

You can test the installation by running `which composer` or `composer`: those command should not raise any error.

.. note::
   See `the composer introduction`_ to learn how to install composer on Mac OS X and Windows

Create your project
^^^^^^^^^^^^^^^^^^^

Create your Chill project using composer:

.. code-block:: bash

   php composer.phar create-project chill-project/standard \
     path/to/your/directory --stability=dev

.. note::
   Until now, the stability of the project is set to "dev". Do not forget this argument, or composer will fail to download and create the project.

Composer will download `the standard architecture`_ and ask you a few question about how to configure your project.

* `database_host` : your postgresql server's address
* `database_port` : the port to reach your postgresql server 
* `database_name` : the name of your database
* `database_user` : the username to reach your database
* `database_password` : your username's password
* `locale`: the language, as iso code. Until now, only fr is supported
* `secret`: the secret string you prepared (see "preparation")

You may accept the default parameters of `debug_toolbar`, `debug_redirects` and `use_assetic_controller` for a demonstration installation. For production, set them all to `false`.

If composer ask you the following question : ::

  Do you want to remove the existing VCS (.git, .svn..) history? [Y,n]?

You may answer `Y` (Yes), or simply press the `return` button.

Launch your server
^^^^^^^^^^^^^^^^^^

If everything was fine, you are able to launch your built-in server :

.. code-block:: bash

   cd path/to/your/directory
   php app/console server:run

Your server should now be available at `http://localhost:8000`. Type this address on your browser and you should see the homepage. 

.. _the composer documentation: https://getcomposer.org/doc/
.. _the composer introduction: https://getcomposer.org/doc/00-intro.md
.. _the standard architecture: https://github.com/Champs-Libres/chill-standard
.. _composer: https://getcomposer.org
.. _Firefox: https://www.mozilla.org
.. _symfony framework: http://symfony.com
