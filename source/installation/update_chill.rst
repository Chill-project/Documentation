.. Copyright (C)  2014 Champs Libres Cooperative SCRLFS
   Permission is granted to copy, distribute and/or modify this document
   under the terms of the GNU Free Documentation License, Version 1.3
   or any later version published by the Free Software Foundation;
   with no Invariant Sections, no Front-Cover Texts, and no Back-Cover Texts.
   A copy of the license is included in the section entitled "GNU
   Free Documentation License".

.. _update-chill:

Update Chill
############

Updating the application is very simple, thanks to the use of composer.

.. code-block:: bash

   cd path/to/your/directory

   composer update
   
Composer allow to control strictly the versions to be used in the application.
This allow to manage a production server, a staging one and a developpement one.

For the production one, define very precisely the versions of each component in the `composer.json` file at the root of the application and require only `stable` versions. 
In this situation even if an update of composer is launched, the sources will not be modified.

For the staging and development one, let open choices to the versions or accept development versions and using `composer update` will automatically update the selected modules. 
Once validated at staging level they can be sent in production re-precising the latest accepted versions.


.. note::
   It is advisable to keep the version of composer up to date. To do that, just launch the following command in a terminal.
     
   .. code-block:: bash
      
      composer self-update
      
   If this update create troubles (which doesn't happen often) you can always undo this update running:
   
   .. code-block:: bash
      
      composer self-update --rollback
      
   See the `the composer documentation`_ for all details.
   

.. _the composer documentation: https://getcomposer.org/doc/
