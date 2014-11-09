.. Copyright (C)  2014 Champs Libres Cooperative SCRLFS
   Permission is granted to copy, distribute and/or modify this document
   under the terms of the GNU Free Documentation License, Version 1.3
   or any later version published by the Free Software Foundation;
   with no Invariant Sections, no Front-Cover Texts, and no Back-Cover Texts.
   A copy of the license is included in the section entitled "GNU
   Free Documentation License".
   
Custom fields bundle
====================

This bundle provide the ability to add custom fields to existing entities.

Those custom fields contains extra data and will be stored in the DB, along with other data's entities. It may be string, text, date, ... or a link to an existing or to-be-created other entities.

In the database, custom fields are stored in json format.

.. seealso::

   The full specification discussed `here. <https://redmine.champs-libres.coop/issues/239>`_
   
   `JSON Type on postgresql documentation <http://www.postgresql.org/docs/9.3/static/datatype-json.html>`_
   
Custom Fields concepts
----------------------

.. warning::

   This section is incomplete
   
Allow custom fields on a entity
--------------------------------

As a developer, you may allow your users to add custom fields on your entities.

Create a json field on your entity
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Declare a json field in your database :

.. code-block:: yaml

   Chill\CustomFieldsBundle\Entity\BlopEntity:
      type: entity
      # ...
      fields:
         customField:
            type: json_array
            
Create the field accordingly :

.. code-block:: php

   namespace Chill\CustomFieldsBundle\Entity;
   
   /**
   * BlopEntity
   */
   class BlopEntity
   {
   
   /**
   * @var array
   */
   private $customField = array();
   
   /**
   * This method will be required for the Form component to record the 
   * custom fields into the class
   *
   * @param array $customField
   * @return BlopEntity
   */
   public function setCustomField(array $customField)
   {
      $this->customField = $customField;
      return $this;
   }
   
   /**
   * Required by Forms to retrieve informations
   *
   * @return array
   */
   public function getCustomField()
   {
      return $this->customField;
   }
            
Declare your customizable class in configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Two methods are available :

* In your app/config.yml file. This is the easiest method, but discouraged because it will reduce the ease for installation.
* In your Configuration class

In app/config.yml file
""""""""""""""""""""""

Add those file under `chill_custom_fields` section :

.. code-block:: yaml

   chill_custom_fields:
      customizables_class:
         - { class: Chill\CustomFieldsBundle\Entity\BlopEntity, name: blop_entity }
         
* The `name` allow you to define a string which is translatable. This string will appears when chill's admin will add/retrieve new customFieldsGroup.
* The class, which is a full FQDN class path


Development tips
-----------------

If you want to test the rendering of a custom fields group, you may use this method :

1. Run the built-in server **from the custom-fields directory** :

.. code-block:: bash

   ./run-server.sh

2. assuming that your custom fields id is `1`, go to your navigator and enter url : `http://localhost:8000/customfieldsgroup/test/render/2`




.. glossary::

   customFieldsGroup
      TODO
      
