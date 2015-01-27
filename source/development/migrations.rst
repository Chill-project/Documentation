.. Copyright (C)  2014 Champs Libres Cooperative SCRLFS
   Permission is granted to copy, distribute and/or modify this document
   under the terms of the GNU Free Documentation License, Version 1.3
   or any later version published by the Free Software Foundation;
   with no Invariant Sections, no Front-Cover Texts, and no Back-Cover Texts.
   A copy of the license is included in the section entitled "GNU
   Free Documentation License".

Database Migrations
********************

Every bundle potentially brings his own database operations : to persist entities, developers have to create database schema, creating indexes,... 

Those schema might be changed (the less is the better) from time to time.

Consequence: each bundle should bring his own migration files, which will bring the database consistent with the operation you will run in your code. They will be gathered into the app installation, ready to be executed by the chill's installer. 

Currently, we use `doctrine migration`_ to manage those migration files. A `composer`_ script located in the **chill standard** component will copy the migrations from your bundle to the doctrne migration's excepted directory after each install and/or update operation.

.. seealso::

   The `doctrine migration`_ documentation
      Learn concepts about migrations files and scripts and the doctrine ORM

   The `doctrine migration bundle`_ documentation
      Learn about doctrine migration integration with Symfony framework

Shipping migration files
========================

Migrations files should be shipped under the Resource/migrations directory. You could customize the migration directory by adding extra information in your composer.json: 

.. code-block:: json

   "extra": {
           "migration-source": "path/to/my/dir"
       }

The class namespace should be `Application\Migrations`, as expected by doctrine migration. Only the files which will be executed by doctrine migration will be moved: they must have the pattern `VersionYYYYMMDDHHMMSS.php` where YYYY is the year, MM the month, DD the day, HH the hour, MM the month and SS the second of creation.

They will be moved automatically by composer when you install or update a bundle.

Executing migration files
==========================

The installers will have to execute migrations files manually, running 

.. code-block:: bash

   php app/console doctrine:migrations:status #will give the current status of the database
   php app/console doctrine:migrations:migrate #process the update


Updating migration files
=========================

.. warning::

   After an installation, migration files will be executed and registered as executed in the database (the version timestamp is recorded into the :title:`migrations_versions` table). If you update your migration file code, the file will still be considered as "executed" by doctrine migration, which will not offers the possibility to run the migration again.

   Consequently, updating migration file should only be considered during development phase, and not published on public git branches. If you want to edit your database schema, you should create a new migration file, with a new timestamp, which will proceed to your schema adaptations.

Every time a migration file is discovered, the composer'script will check if the migration exists in the local migration directory. If yes, the script will compare two file for changes (using a md5 hash). If migrations are discovered, the script will ask the installer to know if he must replace the file or ignore it.

.. note::

   You can manually run composer script by launching `composer run-script post-update-cmd` from your root chill installation's directory.


.. _doctrine migration: http://www.doctrine-project.org/projects/migrations.html
.. _doctrine migration bundle : http://symfony.com/doc/master/bundles/DoctrineMigrationsBundle/index.html
.. _composer : https://getcomposer.org

Tips for development
====================

Migration and data 
------------------

Each time you create a migration script, you should ensure that it will not lead to data losing. Eventually, feel free to use intermediate steps.

Generation
----------

You can generate migration file from the command line, using those commands: 

* `php app/console doctrine:migrations:diff` to generate a migration file by comparing your current database to your mapping information
* `php app/console doctrine:migrations:generate` to generate a blank migration file.

Those files will be located into `app/DoctrineMigrations` directory. You will have to copy those file to your the directory `Resource/migrations` into your bundle directory.

Comments and documentation
--------------------------

As files are copied from your bundle to the `app/DoctrineMigrations` directory, the link between your bundle and the copied file will be unclear. Please add all relevant documentation which will allow future developers to make a link between your file and your bundle.

Inside the script
-----------------

The script which move the migrations files to app directory `might be found here <https://redmine.champs-libres.coop/projects/chill-standard/repository/changes/app/Composer/Migrations.php?rev=master>`_.
