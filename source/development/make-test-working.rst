.. Copyright (C)  2014 Champs Libres Cooperative SCRLFS
   Permission is granted to copy, distribute and/or modify this document
   under the terms of the GNU Free Documentation License, Version 1.3
   or any later version published by the Free Software Foundation;
   with no Invariant Sections, no Front-Cover Texts, and no Back-Cover Texts.
   A copy of the license is included in the section entitled "GNU
   Free Documentation License".

Make tests working
******************

Unit and functional tests are important to ensure that bundle may be deployed securely.

In reason of the Chill architecture, test should be runnable from the bundle's directory and works correctly: this will allow continuous integration tools to run tests automatically. 

.. note::

    Integration tools (i.e. `travis-ci <https://travis-ci.org>`_) works like this : 

    * they clone the bundle repository in a virtual machine, using git
    * they optionnaly run `composer` to download and install depedencies
    * they optionnaly run other command to prepare a database, insert fixtures, ...
    * they run test

On the developer's machine test should be runnable with two or three commands **runned from the bundle directory** : 

.. code-block:: bash

   $ composer install --dev
   $ // command to insert fixtures, ...
   $ phpunit

This chapter has been inspired by `this useful blog post <http://blog.kevingomez.fr/2013/01/09/functional-testing-standalone-symfony2-bundles/>`_.

Bootstrap phpunit for a standalone bundle
==========================================

Unit tests should run after achieving this step.


phpunit.xml
-----------

A `phpunit.xml.dist` file should be present at the bundle root.

.. code-block:: xml

   <?xml version="1.0" encoding="UTF-8"?>

   <phpunit bootstrap="./Tests/bootstrap.php" colors="true">
        <!-- the file "./Tests/boostrap.php" will be created on the next step -->
       <testsuites>
           <testsuite name="ChillMain test suite">
               <directory suffix="Test.php">./Tests</directory>
           </testsuite>
       </testsuites>
       <filter>
           <whitelist>
               <directory>./</directory>
               <exclude>
                   <directory>./Resources</directory>
                   <directory>./Tests</directory>
                   <directory>./vendor</directory>
               </exclude>
          </whitelist>
      </filter>
   </phpunit>


bootstrap.php
--------------

A file `boostrap.php`, located in the `Tests` directory, will allow phpunit to resolve class autoloading :

.. code-block:: php

   <?php

   if (!is_file($autoloadFile = __DIR__.'/../vendor/autoload.php')) {
       throw new \LogicException('Could not find autoload.php in vendor/. Did you run "composer install --dev"?');
   }

   require $autoloadFile;


composer.json
-------------

The `composer.json` file **located at the bundle's root** should contains all depencies needed to run test (and to execute bundle functions).

Ensure that all dependencies are included in the `require` and `require-dev` sections.


Functional tests
================

If you want to access services, database, and run functional tests, you will have to bootstrap a symfony app, with the minimal configuration. Three files are required : 

* a `config_test.yml` file (eventually with a `config.yml`);
* a `routing.yml` file
* an `AppKernel.php` file

Adapt phpunit.xml
-----------------

You should add reference to the new application within `phpunit.xml.dist`. The directive `<php>` should be added like this, if your `AppKernel.php` file is located in `Tests/Fixtures/App` directory:

.. code-block:: xml

    <?xml version="1.0" encoding="UTF-8"?>

    <phpunit bootstrap="./Tests/bootstrap.php" colors="true">
        <testsuites>
            <testsuite name="ChillMain test suite">
                <directory suffix="Test.php">./Tests</directory>
            </testsuite>
        </testsuites>
        <filter>
            <whitelist>
                <directory>./</directory>
                <exclude>
                    <directory>./Resources</directory>
                    <directory>./Tests</directory>
                    <directory>./vendor</directory>
                </exclude>
            </whitelist>
        </filter>
        <!-- the lines we added --> 
       <php>
            <server name="KERNEL_DIR" value="./Tests/Fixtures/App/" />
        </php>
    </phpunit>

AppKernel.php
-------------

This file boostrap the app. It contains three functions. This is the file used in the ChillMain bundle :

.. code-block:: php
                                                                                                                                                                                                                 
    <?php

    use Symfony\Component\HttpKernel\Kernel;
    use Symfony\Component\Config\Loader\LoaderInterface;

    class AppKernel extends Kernel
    {
        public function registerBundles()
        {   
            return array(
                new Symfony\Bundle\FrameworkBundle\FrameworkBundle(),
                new Chill\MainBundle\ChillMainBundle(),
                new Symfony\Bundle\SecurityBundle\SecurityBundle(),
                new Symfony\Bundle\TwigBundle\TwigBundle(),
                new \Symfony\Bundle\AsseticBundle\AsseticBundle(),
                #add here all the required bundle (some bundle are not required)
            );
        }   

        public function registerContainerConfiguration(LoaderInterface $loader)
        {   
            $loader->load(__DIR__.'/config/config_'.$this->getEnvironment().'.yml');
        }   

        /** 
         * @return string
         */
        public function getCacheDir()
        {   
            return sys_get_temp_dir().'/AcmeHelloBundle/cache';
        }   

        /** 
         * @return string
         */
        public function getLogDir()
        {   
            return sys_get_temp_dir().'/AcmeHelloBundle/logs';
        }   
    }

config_test.yml
---------------

There are only few parameters required for the config file. This is the config file for ChillMain :

.. code-block:: yaml

    # config/config_test.yml
    imports:
        - { resource: config.yml } #here we import a config.yml file, this is not required

    framework:
        test: ~
        session:
            storage_id: session.storage.filesystem

.. code-block:: yaml

   # config/config.yml
   framework: 
    secret:          Not very secret
    router:          { resource: "%kernel.root_dir%/config/routing.yml" }
    form:            true
    csrf_protection: true
    session:         ~
    default_locale:  fr  
    translator:      { fallback: fr }
    profiler:        { only_exceptions: false }
    templating: #required for assetic. Remove if not needed
        engines: ['twig']

.. note:: 

    You must adapt config.yml file according to your required bundle. Some options will be missing, other may be removed...

.. note::

    If you would like to tests different environments, with differents configuration, you could create differents config_XXX.yml files.

routing.yml
------------

You should add there all routing information needed for your bundle. 

.. code-block: yaml

    chill_main_bundle:
      resource: "@CLChillMainBundle/Resources/config/routing.yml"

That's it. Tests should not pass.
