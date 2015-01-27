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
* In your Extension class : harder for devs, easier for installers.

In app/config.yml file
""""""""""""""""""""""

Add those file under `chill_custom_fields` section :

.. code-block:: yaml

   chill_custom_fields:
      customizables_entities:
         - { class: Chill\CustomFieldsBundle\Entity\BlopEntity, name: blop_entity }
         
* The `name` allow you to define a string which is translatable. This string will appears when chill's admin will add/retrieve new customFieldsGroup.
* The class, which is a full FQDN class path

Automatically, in DepedencyInjection/Extension class
""""""""""""""""""""""""""""""""""""""""""""""""""""

.. todo::

   Explain how to declare customizable entitites in DepedencyInjection/Extension.

Rendering custom fields in a template
--------------------------------------

Two function are available :

* `chill_custom_field_widget` to render the widget. This function is defined on a customFieldType basis.
* `chill_custom_field_label` to render the label. You can customize the label rendering by choosing the layout you would like to use.

**chill_custom_field_label**

The signature is :

* `CustomField|object|string` **$customFieldOrClass** either a customField OR a customizable_entity OR the FQDN of the entity
*  `string` **$slug** only necessary if the first argument is NOT a CustomField instance
* `array` **params** the parameters for rendering. Currently, 'label_layout' allow to choose a different label. Default is 'ChillCustomFieldsBundle:CustomField:render_label.html.twig'

Examples

.. code-block:: jinja

   {{ chill_custom_field_label(customField) }}

   {{ chill_custom_field_label(entity, 'slug') }}

   {{ chill_custom_field_label('Path\To\Entity', 'slug') }}


**chill_custom_field_widget**

*  array **$fields** the array raw, as stored in the db
*  CustomField|object|string $customFieldOrClass either a customField OR a customizable_entity OR the FQDN of the entity
*  string **$slug** only necessary if the first argument is NOT a CustomField instance

Examples:

.. code-block:: jinja

   {{ chill_custom_field_widget(entity.customFields, customField) }}

   {{ chill_custom_field_widget(entity.customFields, entity, 'slug') }}

   {{ chill_custom_field_widget(fields, 'Path\To\Entity', 'slug') }}

.. warning::

   This feature is not fully tested. See `the corresponding issue <https://redmine.champs-libres.coop/issues/283>`_


Custom Fields's form
---------------------

You should simply use the 'custom_field' type in a template, with the group you would like to render in the `group` option

Example : 

.. warning::

   The above code isn't tested.

.. todo:: 

   Test the above code.

.. code-block:: php

   use Symfony\Component\Form\AbstractType;
   use Symfony\Component\Form\FormBuilderInterface;
   use Symfony\Component\OptionsResolver\OptionsResolverInterface;
   use Chill\CustomFieldsBundle\Form\Type\CustomFieldType;
   use Chill\CustomFieldsBundle\Entity\CustomFieldsGroup;

   class BlopEntityType extends AbstractType
   {

      public $group;

      public function __construct(CustomFieldsGroup $group)
      {
         $this->group = $group;
      }


       /**
        * @param FormBuilderInterface $builder
        * @param array $options
        */
      public function buildForm(FormBuilderInterface $builder, array $options)
      {
         $builder
            ->add('field1')
            ->add('field2')
            //->add('adress', new AdressType())
            ->add('customField', 'custom_field', array('group' => $group))
           ;
       }
   }




Development tips
-----------------

If you want to test the rendering of a custom fields group, you may use this method :

1. Run the built-in server **from the custom-fields directory** :

.. code-block:: bash

   ./run-server.sh

2. assuming that your custom fields id is `1`, go to your navigator and enter url : `http://localhost:8000/customfieldsgroup/test/render/1`




.. glossary::

   customFieldsGroup
      TODO
      
