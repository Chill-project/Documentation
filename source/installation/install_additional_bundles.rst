.. Copyright (C)  2014 Champs Libres Cooperative SCRLFS
   Permission is granted to copy, distribute and/or modify this document
   under the terms of the GNU Free Documentation License, Version 1.3
   or any later version published by the Free Software Foundation;
   with no Invariant Sections, no Front-Cover Texts, and no Back-Cover Texts.
   A copy of the license is included in the section entitled "GNU
   Free Documentation License".

.. _install-additional-bundles:

Install additional bundles
##########################

A basic installation of Chill include four bundles: 

   - :ref:`main-bundle`
   - :ref:`person-bundle`
   - :ref:`report-bundle`
   - :ref:`custom-fields-bundle`
   
but you can add as many as needed by your project, and if the bundle does not exists yet, you can create a new one, see :ref:`create-new-bundle` .
   
In Chill you are free to do what is most suitable for your activity, so let's go into details on how to add an existing bundle.

We will add the bundle 'icpc2' that set `icpc code <https://en.wikipedia.org/wiki/International_Classification_of_Primary_Care>`_ available as custom field.

.. todo::
   Add description of the bundle
   
Open your terminal let composer do the magic for you:

.. code-block:: bash

   cd path/to/your/directory   
   composer require chill-main/icpc2

As composer ends its task, it could notify you that `Some migration files have been imported. 
In this case You should run `php app/console doctrine:migrations:status` and/or `php app/console doctrine:migrations:migrate` to apply them to your DB.` 

So just do it:

.. code-block:: bash

   php app/console doctrine:migrations:migrate

.. note::
   The following  has to be automated:
   
   Finally we should modify the AppKernel.php file adding `new Chill\\Icpc2Bundle\\ChillIcpc2Bundle(),` in the $bundle array as described `in the symfony documentation <http://symfony.com/doc/current/cookbook/bundles/installation.html#b-enable-the-bundle>`_.
      
