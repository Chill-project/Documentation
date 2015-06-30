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

We are going to describe a basic installation on Unix systems (Unix, Mac OS). Windows installation has not been tested.

Requirements
------------

Client requirements
^^^^^^^^^^^^^^^^^^^

Chill is accessible through a web browser. Currently, we focus our support on `Firefox`_, because firefox is open source, cross-platform, and very well active. The software should work with other browser (Chromium, Opera, ...) but some functionalities might break.

Server requirements
^^^^^^^^^^^^^^^^^^^

* a postgresql database, with the `*unaccent* extension`_ enabled. The minimum version is postgresql 9.4. You can use `the docker image provided <https://registry.hub.docker.com/u/chill/database/>`_. Using the docker image is also a solution for production site. Alternatively you can install a PosgresSql server see :ref:`install-postgres-server`.
* PHP 5.5.
* If you run Chill in production mode, you should also install a web server (apache, ngnix, ...) see :ref:`install-production-webserver`. For this basic installation meant for testing and/or development, we will make it simplier using the php built-in server.


Let's start by installing and configuring the docker database.
You will find all details concerning the installation of docker on their official site looking for your OS into the menu `Install/Docker Engine <http://docs.docker.com/>`_. 

Once docker is installed, run : 

.. code-block:: bash

   sudo docker run -P --name=chill_db chill/database

This will download the chill/database image and start a new docker instance with the name `chill_db` and export the postgresql port `5432` on another random local port. 

.. _docker-database-port:

The docker database port
^^^^^^^^^^^^^^^^^^^^^^^^

Open a new terminal and run 

.. code-block:: bash

   sudo docker port chill_db 5432 

This command will show on which port the docker container is listening, on your localhost. During the part :ref:`create-your-project` this is the value to be used to fill the field 'database_port' as explained hereafter. 

Preparation
-----------

To create your project, you will need the following information :

* how to access to your database: host, port, database name, and your credentials (username and password) ;
* a random string, which will be used to improve entropy in security. Choose anything you want (random character, your father's birthplace, ...).

If you have installed the docker database your information should be:
    - database_host: 127.0.0.1
    - database_port: see :ref:`docker-database-port`
    - database_name: postgres
    - database_user: postgres
    - database_password: postgres
    - locale: fr

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

Install composer.phar globally
""""""""""""""""""""""""""""""

.. note::
   This part is not mandatory, if you do not want to install composer globally, you will have to replace in the commands of this tutorial `composer` by `php composer.phar`.

Install composer globally on you system will made the installation process easier. To do this, simply run 

.. code-block:: bash

   sudo mv composer.phar /usr/local/bin/composer

You can test the installation by running `which composer` or `composer`: those command should not raise any error.

.. note::
   See `the composer introduction`_ to learn how to install composer on Mac OS X and Windows

.. _create-your-project:

Create your project
^^^^^^^^^^^^^^^^^^^

Create your Chill project using composer:

.. code-block:: bash

   composer create-project chill-project/standard path/to/your/directory --stability=dev

You should, now, move your cursor to the new directory

.. code-block:: bash

   cd path/to/your/directory

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

.. note::

   If composer ask you the following question : ::

     Do you want to remove the existing VCS (.git, .svn..) history? [Y,n]?

   You may answer `Y` (Yes), or simply press the `return` button.

.. note::

   At the end of the installation, composer will warn you to execute database migration script, with this message : ::

     Some migration files have been imported. You should run 
     `php app/console doctrine:migrations:status` and/or 
     `php app/console doctrine:migrations:migrate` to apply them to your DB.

   We will proceed this step a bit later. See :ref:`create-database-schema`.

Check your requirements
^^^^^^^^^^^^^^^^^^^^^^^

You should check your installation by running 

.. code-block:: bash

   php app/check.php

Which will give you information about how your installation fullfill the requirements to running Chill, and give you advices to optimize your installation.


.. _create-database-schema:

Create your database schema
^^^^^^^^^^^^^^^^^^^^^^^^^^^

This step will create your table and minimum information into your database. Simply run 

.. code-block:: bash

   php app/console doctrine:migrations:migrate

SQL queries will be printed into your console.


Populate your database with basic information
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Once your database schema is ready, you should populate your database with some basic information. Those are provided through scripts and might depends from the bundle you choose to install (see :ref:`install-additional-bundles`)

The main bundle require two scripts to be executed : 

.. code-block:: bash

   php app/console doctrine:fixtures:load
   php app/console chill:main:countries:populate 
   php app/console chill:main:languages:populate

Those will populate database, respectively, with countries (using ISO declaration) and languages (using, also, ISO informations).


Building CSS (optionnal)
^^^^^^^^^^^^^^^^^^^^^^^^

For this step, npm must be installed.

A build version of the needed CSS file is provided within the main bundle `Resources/public/css/chillmain.css`. For rebuilding it :


.. code-block:: bash

   #in the main bundle directory ( vendor/chill-project/main/ )
   cd vendor/chill-project/main/Resources/
   npm install grunt


Go back to your project root before doing next step

.. code-block:: bash

   cd path/to/your/directory


Launch your server
^^^^^^^^^^^^^^^^^^

If everything was fine, you are able to launch your built-in server :

.. code-block:: bash

   php app/console server:run

Your server should now be available at `http://localhost:8000`. Type this address on your browser and you should see the homepage.
The default login is 'center a_social' with password 'password'.

Have fun exploring Chill.


Uninstall Chill
```````````````

.. todo::

   the section "Uninstall Chill" must be written. Help appreciated :-)
   
Uninstall the docker database
-----------------------------

Uninstall the application
-------------------------
   

.. _the composer documentation: https://getcomposer.org/doc/
.. _the composer introduction: https://getcomposer.org/doc/00-intro.md
.. _the standard architecture: https://github.com/Champs-Libres/chill-standard
.. _composer: https://getcomposer.org
.. _Firefox: https://www.mozilla.org
.. _symfony framework: http://symfony.com
.. _*unaccent* extension: http://www.postgresql.org/docs/current/static/unaccent.html
