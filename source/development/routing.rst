.. Copyright (C)  2014 Champs Libres Cooperative SCRLFS
   Permission is granted to copy, distribute and/or modify this document
   under the terms of the GNU Free Documentation License, Version 1.3
   or any later version published by the Free Software Foundation;
   with no Invariant Sections, no Front-Cover Texts, and no Back-Cover Texts.
   A copy of the license is included in the section entitled "GNU
   Free Documentation License".


Routing
#######

Our goal is to ease the installation of the different bundle. Users should not have to dive into complicated config files to install bundles.

A routing loader available for all bundles
===========================================

A Chill bundle may rely on the Routing Loader defined in ChillMain. 

The loader will load `yml` or `xml` files. You simply have to add them into `chill_main` config

.. code-block:: yaml

   chill_main:
   # ... other stuff here
      routing:
         resources:
            - @ChillMyBundle/Resources/config/routing.yml

Load routes automatically
-------------------------

But this force users to modify config files. To avoid this, you may prepend config implementing the `PrependExtensionInterface` in the `YourBundleExtension` class. This is an example from **chill main** bundle : 


.. code-block:: php

   namespace Chill\MainBundle\DependencyInjection;

   use Symfony\Component\DependencyInjection\ContainerBuilder;
   use Symfony\Component\HttpKernel\DependencyInjection\Extension;
   use Symfony\Component\DependencyInjection\Extension\PrependExtensionInterface;

   class ChillMainExtension extends Extension implements PrependExtensionInterface
   {

       public function load(array $configs, ContainerBuilder $container)
       {
           // ...
       }

       public function prepend(ContainerBuilder $container) 
       {

           //add current route to chill main
           //this is where the resource is added automatically in the config
           $container->prependExtensionConfig('chill_main', array(
              'routing' => array(
                 'resources' => array(
                    '@ChillMainBundle/Resources/config/routing.yml'
                 )

              )
           ));
       }
   }


