.. Copyright (C)  2014 Champs Libres Cooperative SCRLFS
   Permission is granted to copy, distribute and/or modify this document
   under the terms of the GNU Free Documentation License, Version 1.3
   or any later version published by the Free Software Foundation;
   with no Invariant Sections, no Front-Cover Texts, and no Back-Cover Texts.
   A copy of the license is included in the section entitled "GNU
   Free Documentation License".
   
.. _custom-fields-bundle:

Custom fields bundle
====================

This bundle provides the ability to add custom fields to existing entities.

Those custom fields contains extra data and will be stored in the DB, along with other data's entities. It may be string, text, date, ... or a link to an existing or to-be-created other entities.

In the database, custom fields are stored in json format.

.. seealso::

   The full specification discussed `here. <https://redmine.champs-libres.coop/issues/239>`_
   
   `JSON Type on postgresql documentation <http://www.postgresql.org/docs/9.3/static/datatype-json.html>`_
   
Custom Fields concepts
----------------------

Custom fields are extra data which may be added to entities by user. If a developer implements custom fields on a entity, users will be able to add more fields on this entity.

Example: the `person bundle` allows to record `firstname`, `lastname`, `date of birth` fields. But users need to store information about the kind of house he has (if he owns his house, if he rents it, ...). Custom fields allows to create those fields.

Automatically, those fields are added at the person form. They are also printed in the person view and in exports.

Custom fields and custom fields group
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Custom fields are associated to a custom fields group. When a user want to add custom fields on an entity, he must first create a custom fields group and associate this field with an entity. Then he may add add custom fields to this groups. 

Some entities needs a **default** custom fields group. For instance, the default custom fields group will be printed on the main form for person, and will be appended on the main person view. Some bundle does not use this feature (i.e. the `report` bundle).

.. note::

   In the future of the `person bundle`, other custom fields group will be added in forms accessible from the menu, allowing users to completely customize and separate their entities.
   
Allow custom fields on an entity
--------------------------------

As a developer, you must allow your users to add custom fields on your entities.

.. warning::
    For having custom fields, the class of the entity must contain a variable for storing the custom data. **By convention this variable must be called $cFData**


Create a json field on your entity
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Declare a json field in your database :

.. code-block:: yaml

   Chill\CustomFieldsBundle\Entity\BlopEntity:
      type: entity
      # ...
      fields:
         cFData:
            type: json_array
            
Create the field accordingly in the class logic :

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
   private $cFData;
   
   /**
   * You must set a setter in order to save automatically custom 
   * fields from forms, using Form Component
   *
   * @param array $cFData
   * @return BlopEntity
   */
   public function setCFData(array $cFData)
   {
      $this->cFData = $cFData;
      return $this;
   }
   
   /**
   * You also must create a getter in order to let Form 
   * component populate form fields
   *
   * @return array
   */
   public function getCFData()
   {
      return $this->cFData;
   }
            
Declare your customizable entity in configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This step is necessary to allow user to create custom fields group associated with this entity.

Two methods are available.

The recommended method is to do it in DependencyInjection/Extension class. It is recommended as your bundle will be well set up (for custom field) in every chill installation that use it.

The discouraged method is to declare via the app/config.yml file. This is very quick to set up for a given chill installation but it is not done automatically : in every chill installation that use your bundle, this step has to be performed.

In app/config.yml file (discouraged)
""""""""""""""""""""""""""""""""""""

This method is discouraged but explained first as it helps to undersand the recommended method.

Add those file under `chill_custom_fields` section :

.. code-block:: yaml

   chill_custom_fields:
      customizables_entities:
         - { class: Chill\YourBundleBundle\Entity\BlopEntity, name: blop_entity }
         
* The `name` allow you to define a string which is translatable. This string will appears when chill's admin will add/retrieve new customFieldsGroup.
* The class, which is a full FQDN class path

Automatically, in DependencyInjection/Extension class (recommended)
"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""

This is the recommended way for declaring customizable classes. 

You can prepend configuration of `custom fields bundle` from the class `YourBundle\DependencyInjection\YourBundleExtension`. **Note** that you also have to implements `Symfony\Component\DependencyInjection\Extension\PrependExtensionInterface` on this class to make the `prepend` function being taken into account.

Example here : 

.. code-block:: php

    class ChillYourBundleExtension extends Extension implements PrependExtensionInterface
    {
        /**
         * @param ContainerBuilder $container
         */
        public function prepend(ContainerBuilder $container)
        {
            $bundles = $container->getParameter('kernel.bundles');
            if (!isset($bundles['ChillCustomFieldsBundle'])) {
                throw new MissingBundleException('ChillCustomFieldsBundle');
            }

            $container->prependExtensionConfig('chill_custom_fields',
                array('customizables_entities' => 
                    array(
                        array(
                            'class' => 'Chill\YourBundleBundle\Entity\BlopEntity', 
                            'name' => 'blop_entity',)
                   )
               )
            );
        }
    }

* The `name` allow you to define a string which is translatable. This string will appears when chill's admin will add/retrieve new customFieldsGroup.
* The class, which is a full FQDN class path

.. seealso::

   `How to simplify configuration of multiple bundles <http://symfony.com/doc/current/cookbook/bundles/prepend_extension.html>`_
      A cookbook page about prepending configuration.


Adding options to your custom fields groups
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You may add options to the groups associated with an entity.

In `config.yml` the declaration should be : 

.. code-block:: yaml

   chill_custom_fields:
       customizables_entities:
           - 
               class: Chill\YourBundleBundle\Entity\BlopEntity
               name: BlopEntity
               options:
                   # this will create a "myFieldKey" field as text, with a maxlength attribute to 150 (see http://symfony.com/doc/master/reference/forms/types/text.html)
                   myFieldKey: {form_type: text, form_options: {attr: [maxlength: 150]}} 

In the `PrependExtensionInterface::prepend` function, the options key will be added in the configuration definition : 

.. code-block:: php

   class ChillYourBundleExtension extends Extension implements PrependExtensionInterface
   {
       /**
        * @param ContainerBuilder $container
        */
       public function prepend(ContainerBuilder $container)
       {
           $bundles = $container->getParameter('kernel.bundles');
           if (!isset($bundles['ChillCustomFieldsBundle'])) {
               throw new MissingBundleException('ChillCustomFieldsBundle');
           }

           $container->prependExtensionConfig('chill_custom_fields',
               array('customizables_entities' => 
                   array(
                       array(
                          'class' => 'Chill\YourBundleBundle\Entity\BlopEntity', 
                          'name' => 'BlopEntity',
                          'options' => array(
                                'myFieldKey' => [ 'form_type' => 'text', 'form_options' => [ 'attr' => [ 'maxlength' => 150 ] ]
                          ))
                   )
               )
           );
       }
   }
               
**Example :** the entity `Report` from **ReportBundle** has to pick some custom fields belonging to a group to print them in *summaries* the timeline page. The definition will use the special type `custom_fields_group_linked_custom_field` which will add a select input with all fields associated with the current custom fields group : 

.. code-block:: php

   class ChillReportExtension extends Extension implements PrependExtensionInterface
   {
       /**
        * 
        * 
        * @param ContainerBuilder $container
        */
       public function prepend(ContainerBuilder $container)
       {
           $bundles = $container->getParameter('kernel.bundles');
           if (!isset($bundles['ChillCustomFieldsBundle'])) {
               throw new MissingBundleException('ChillCustomFieldsBundle');
           }

           $container->prependExtensionConfig('chill_custom_fields',
               array('customizables_entities' => 
                   array(
                       array(
                          'class' => 'Chill\ReportBundle\Entity\Report', 
                          'name' => 'ReportEntity',
                          'options' => array(
                             'summary_fields' => array(
                                'form_type' => 'custom_fields_group_linked_custom_fields',
                                'form_options' => 
                                   [
                                      'multiple' => true,
                                      'expanded' => false
                                   ]
                             )
                          ))
                   )
               )
           );
       }
   }

Note that `custom_fields_group_linked_custom_fields` does not create any input on `CustomFieldsGroup` creation : there aren't any fields associated with the custom fields just after the group creation... You have to add custom fields and associate them with the newly created group to see them appears.

Rendering custom fields and custom fields group in a template
-------------------------------------------------------------

.. warning::
    Each custom field can be `active` or not. Only `active` custom fields has to be dislayed.

For rendering custom fields, two function are available :

* `chill_custom_field_widget` to render the widget. This function is defined on a customFieldType basis.
* `chill_custom_field_label` to render the label. You can customize the label rendering by choosing the layout you would like to use.

For rendering custom fields group, a function is available :

* `chill_custom_fields_group_widget to render the widget. It will display the custom fields of the group in a dd / dt structure.

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

The signature is :

*  array **$fields** the array raw, as stored in the db
*  CustomField|object|string **$customFieldOrClass** either a customField OR a customizable_entity OR the FQDN of the entity
*  string **$slug** only necessary if the first argument is NOT a CustomField instance

Examples:

.. code-block:: jinja

   {{ chill_custom_field_widget(entity.customFields, customField) }}

   {{ chill_custom_field_widget(entity.customFields, entity, 'slug') }}

   {{ chill_custom_field_widget(fields, 'Path\To\Entity', 'slug') }}

.. warning::

   This feature is not fully tested. See `the corresponding issue <https://redmine.champs-libres.coop/issues/283>`_


**chill_custom_fields_group_widget**

This function only display custom fields that are `active.

The signature is :

*  array **$fields** the array raw, as stored in the db
*  CustomFieldsGroup **$customFieldsGroup** the custom field group to render

.. code-block:: jinja

   {{ chill_custom_fields_group_widget(entity.cFData, entity.customFieldsGroup) }}

Custom Fields's form
--------------------

You should simply use the 'custom_field' type in a template, with the group you would like to render in the `group` option's type.

Example : 

.. code-block:: php

   namespace Chill\ReportBundle\Form;

   use Symfony\Component\Form\AbstractType;
   use Symfony\Component\Form\FormBuilderInterface;
   use Symfony\Component\OptionsResolver\OptionsResolverInterface;

   class ReportType extends AbstractType
   {
       /**
        * @param FormBuilderInterface $builder
        * @param array $options
        */
       public function buildForm(FormBuilderInterface $builder, array $options)
       {
           $entityManager = $options['em'];

           $builder
               ->add('user')
               ->add('date', 'date', 
                   array('required' => true, 'widget' => 'single_text', 'format' => 'dd-MM-yyyy'))
               #add the custom fields :
               ->add('cFData', 'custom_field', 
                   array('attr' => array('class' => 'cf-fields'), 'group' => $options['cFGroup']))
           ;
       }
       
       /**
        * @param OptionsResolverInterface $resolver
        */
       public function setDefaultOptions(OptionsResolverInterface $resolver)
       {
           $resolver->setDefaults(array(
               'data_class' => 'Chill\ReportBundle\Entity\Report'
           ));

           $resolver->setRequired(array(
               'em',
               'cFGroup',
           ));

           $resolver->setAllowedTypes(array(
               'em' => 'Doctrine\Common\Persistence\ObjectManager',
               'cFGroup' => 'Chill\CustomFieldsBundle\Entity\CustomFieldsGroup'
           ));
       }

       /**
        * @return string
        */
       public function getName()
       {
           return 'chill_reportbundle_report';
       }
   }




Development tips
----------------

If you want to test the rendering of a custom fields group, you may use this method :

1. Run the built-in server **from the custom-fields directory** :

.. code-block:: bash

   ./run-server.sh

2. assuming that your custom fields id is `1`, go to your navigator and enter url : `http://localhost:8000/customfieldsgroup/test/render/1`

      
