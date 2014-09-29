
Requirements
============

* a postgresql database. The minimum version is postgresql 9.1, but we are working on developing on the 9.4 branch, which will provide features which will ease developper work
* php 5.5

You won't need any web server for demonstration or development.

Preparation
===========

Prepare :

* your credentials to use your postgresql database
* a random string, which will be use to improve entropy in security. Choose anything you want (random character, your father's birthplace, ...)

Installation
=============

Chill is installed with composer.

Install composer
----------------

..  note::
  If you do not know composer, it is a good idea to have a glance at `the composer documentation`_ 

Install composer on your system :

.. code-block:: bash

   curl -sS https://getcomposer.org/installer | php

move composer.phar to your system (optional)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Install composer globally on you system will made the installation process more convenient. To do this, simply run 

.. code-block:: bash
   sudo mv composer.phar /usr/local/bin/composer

Then, just run `composer` instead of `php composer.phar`

.. note::
   See `the composer introduction`_ to learn how to install composer on Mac OS X and Windows

Create your project
-------------------

Create your Chill project using composer:

.. code-block:: bash
   php composer.phar create-project champs-libres/chill-standard path/to/your/directory --stability=dev

.. note::
   Until now, the stability of the project is set to "dev". Do not forget this argument, or composer will fail to download and create the project.

Composer will download `the standard architecture`_ and ask you a few question about how to configure your project.

* **database_host** : your postgresql server's address
* **database_port** : the port to reach your postgresql server 
* **database_name** : the name of your database
* **database_user** : the username to reach your database
* **database_password** : your username's password
* **locale**: the language, as iso code. Until now, only fr is supported
* **secret**: the secret string you prepared (see "preparation")

You may accept the default parameters of **debug_toolbar**, **debug_redirects** and **use_assetic_controller** for a demonstration installation. For production, set them all to `false`.

Launch your server
-------------------

If everything was fine, you are able to launch your built-in server :

.. code-block::bash
   php app/console server:run

Your server should now be available at `http://localhost:8000`

.. _the composer documentation: https://getcomposer.org/doc/
.. _the composer introduction: https://getcomposer.org/doc/00-intro.md
