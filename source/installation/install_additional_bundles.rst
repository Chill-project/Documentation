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

There are four available bundles.

   - Main bundle
   - Custom Fields bundle
   - Person bundle
   - Report bundle
   
In Chill you are free to do what is most suitable for your activity. 
By the way, the `Main bundle` is highly **required** as it provides the access control model (users, groups, and all concepts). So it should be installed.
The other bundles are optional, and if you decide to use them their installation will follow the same mechanism as the Main.

So let's go into details on how to install the Main bundle.

The Installation of the Chill project has already prepared everything to make this easy.
Open your terminal and run the followings:

.. code-block:: bash

   cd path/to/your/directory
   
   # Go to the placeholder directory of the desired bundle (in this case `main`).
   
   cd vendor/chill-project/main
   
   # Install the bundle with composer:
   
   composer install

As composer ends its task, it will notify you that `Some migration files have been imported. 
You should run `php app/console doctrine:migrations:status` and/or `php app/console doctrine:migrations:migrate` to apply them to your DB.` 

So just do it:

.. code-block:: bash

   cd path/to/your/directory

   php app/console doctrine:migrations:status
   php app/console doctrine:migrations:migrate
   
To be sure that install of the bundle has been done correctly, we will launch the unit tests related to it.
  
.. code-block:: bash
   
   cd vendor/chill-project/main
   phpunit
   