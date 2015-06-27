.. Copyright (C)  2015 Champs Libres Cooperative SCRLFS
   Permission is granted to copy, distribute and/or modify this document
   under the terms of the GNU Free Documentation License, Version 1.3
   or any later version published by the Free Software Foundation;
   with no Invariant Sections, no Front-Cover Texts, and no Back-Cover Texts.
   A copy of the license is included in the section entitled "GNU
   Free Documentation License".

Access controle model
**********************

.. contents:: Table of content
    :local:

Concepts
========

Every time an entity is created, viewed or updated, the software check if the user has the permission to make this action. The decision is made with three parameters :

- the type of entity ;
- the entity's center ;
- the entity'scope

The user must be granted access to the action on this particular entity, with this scope and center.

From an user point of view
--------------------------

The software is design to allow fine tuned access rights for complicated installation and team structure. The administrators may also decide that every user has the right to see all resources, where team have a more simple structure. 

Here is an overview of the model.

Chill can be multi-center
^^^^^^^^^^^^^^^^^^^^^^^^^

Chill is designed to be installed once for social center who work with multiple teams separated, or for social services's federation who would like to share the same installation of the software for all their members. 

This was required for cost reduction, but also to ease the generation of statistics agregated across federation's members, or from the central unit of the social center with multiple teams.

Otherwise, it is not required to create multiple center: Chill can also work for one center.

Obviously, users working in the different centers are not allowed to see the entities (_persons_, _reports_, _activities_) of other centers. But users may be attached to multiple centers: consequently they will be able to see the entities of the multiple centers they are attached to.

Inside center, scope divide team
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Users are attached to one or more center and, inside to those center, there may exists differents scopes. The aim of those _scopes_ is to divide the whole team of social worker amongst different departement, for instance: the social team, the psychologist team, the nurse team, the administrative team, ... Each team is granted of different rights amongst scope. For instance, the social team may not see the _activities_ of the psychologist team. The administrative team may see the date & time's activities, but is not allowed to see the detail of those entities (the personal notes, ...).

The administrator is responsible of creating those scopes and team. *He may also decide to not define a division inside his team*: he creates only one scope and all entities will belong to this scope, all users will be able to see all entities.

As entities have only one scopes, if some entities must be shared across two different teams, the administrator will have to create a scope *shared* by two different team, and add the ability to create, view, or update this scope to those team. 

Example: if some activities must be seen and updated between nurses and psychologists, the administrator will create a scope "nurse and psy" and add the ability for both team "nurse" and "psychologist" to "create", "see", and "update" the activities belonging to scope "nurse and psy".

The concepts translated into code
-----------------------------------

.. figure:: /static/access_control_model.png
   
   Schema of the access control model

Chill handle **entities**, like *persons*, *reports* (associated to *persons*), *activities* (also associated to *_persons*), ... On creation, those entities are linked to one center and, eventually, to one scope. They implements the interface `HasCenterInterface`.

.. note::

   Somes entities are linked to a center through the entity they are associated with. For instance, *activities* or *reports* are associated to a *person*, and the person is associated to a *center*. The *report*'s *center* is always the *person*'s *center*.

Entities may be associated with a scope. In this case, they implement the `HasScopeInterface`.

.. note::

   Currently, only the *person* entity is not associated with a scope. 

At each step of his lifetime (creation, view of the entity and eventually of his details, update and, eventually, deletion), the right of the user are checked. To decide wether the user is granted right to execute the action, the software must decide with those elements :

- the entity's type;
- the entity's center ;
- the entity's scope, if it exists,
- and, obviously, a string representing the action

All those action are executed through symfony voters and helpers. 

How to check authorization ?
----------------------------

Just use the symfony way-of-doing, but do not forget to associate the entity you want to check access. For instance, in controller :  

.. code-block:: php

   class MyController extends Controller 
   {

      public function viewAction($entity)
      {
         $this->denyAccessUnlessGranted('CHILL_ENTITY_SEE', $entity);

         //... go on with this action
      }
   }

And in template :

.. code-block:: html+jinja

   {{ if is_granted('CHILL_ENTITY_SEE', entity) %}print something{% endif %}

Retrieving reachable scopes and centers
----------------------------------------

The class :class:`Chill\\MainBundle\\Security\\Authorization\\AuthorizationHelper` helps you to get centers and scope reachable by a user.

Those methods are intentionnaly build to give information about user rights: 

- getReachableCenters: to get reachable centers for a user
- getReachableScopes : to get reachable scopes for a user

.. note::

   The service is reachable through the Depedency injection with the key `chill.main.security.authorization.helper`. Example :

   .. code-block:: php

      $helper = $container->get('chill.main.security.authorization.helper');

.. todo::

   Waiting for a link between our api and this doc, we invite you to read the method signatures `here <https://github.com/Chill-project/Main/blob/add_acl/Security/Authorization/AuthorizationHelper.php>`_

Adding your own roles
=====================

.. warning::

   This part is not fully implemented. The signature of the abstract class :class:`Chill\\Security\\Authorization\\ChillVoter` will change in the future.

Extending Chill will requires you to define your own roles and rules for your entities. You will have to define your own voter to do so.

.. seealso::

   `How to Use Voters to Check User Permissions <http://symfony.com/doc/current/cookbook/security/voters_data_permission.html>`_

   From the symfony cookbook

   `New in Symfony 2.6: Simpler Security Voters <http://symfony.com/blog/new-in-symfony-2-6-simpler-security-voters>`_

   From the symfony blog



To create your own roles, you will have to implement your own voter. This voter will have to extends the :class:`Chill\\MainBundle\\Security\\AbstractChillVoter`. Inside this class, you might use the :class:Chill\\MainBundle\\Security\\Authorization\\AuthorizationHelper to check permission (do not re-invent the wheel). This is a real-world example:

.. code-block:: php

   namespace Chill\ReportBundle\Security\Authorization;
   use Chill\MainBundle\Security\Authorization\AbstractChillVoter;
   use Chill\MainBundle\Security\Authorization\AuthorizationHelper;


   class ReportVoter extends AbstractChillVoter
   {
       const CREATE = 'CHILL_REPORT_CREATE';
       const SEE    = 'CHILL_REPORT_SEE';
       const UPDATE = 'CHILL_REPORT_UPDATE';
       
       /**
        *
        * @var AuthorizationHelper
        */
       protected $helper;
       
       public function __construct(AuthorizationHelper $helper)
       {
           $this->helper = $helper;
       }
       
       protected function getSupportedAttributes()
       {
           return array(self::CREATE, self::SEE, self::UPDATE);
       }
       protected function getSupportedClasses()
       {
           return array('Chill\ReportBundle\Entity\Report');
       }
       protected function isGranted($attribute, $report, $user = null)
       {
           if (! $user instanceof \Chill\MainBundle\Entity\User){
               
               return false;
           }
           
           return $this->helper->userHasAccess($user, $report, $attribute);
       }
   }

Then, you will have to declare the service and tag it as a voter :

.. code-block:: yaml

   services:
       chill.report.security.authorization.report_voter:
           class: Chill\ReportBundle\Security\Authorization\ReportVoter
           arguments:
               - "@chill.main.security.authorization.helper"
           tags:
            - { name: security.voter }


Adding role hierarchy
---------------------

You should prepend Symfony's security component directly from your code. 

.. code-block:: php

   namespace Chill\ReportBundle\DependencyInjection;
   use Symfony\Component\DependencyInjection\ContainerBuilder;
   use Symfony\Component\Config\FileLocator;
   use Symfony\Component\HttpKernel\DependencyInjection\Extension;
   use Symfony\Component\DependencyInjection\Loader;
   use Symfony\Component\DependencyInjection\Extension\PrependExtensionInterface;
   use Chill\MainBundle\DependencyInjection\MissingBundleException;

   /**
    * This is the class that loads and manages your bundle configuration
    *
    * To learn more see {@link http://symfony.com/doc/current/cookbook/bundles/extension.html}
    */
   class ChillReportExtension extends Extension implements PrependExtensionInterface
   {
       public function prepend(ContainerBuilder $container)
       {
           $this->prependRoleHierarchy($container);
       }

       protected function prependRoleHierarchy(ContainerBuilder $container)
       {
           $container->prependExtensionConfig('security', array(
              'role_hierarchy' => array(
                 'CHILL_REPORT_UPDATE' => array('CHILL_REPORT_SEE'),
                 'CHILL_REPORT_CREATE' => array('CHILL_REPORT_SEE')
              )
           ));
       }
   }


