.. Copyright (C)  2015 Champs Libres Cooperative SCRLFS
   Permission is granted to copy, distribute and/or modify this document
   under the terms of the GNU Free Documentation License, Version 1.3
   or any later version published by the Free Software Foundation;
   with no Invariant Sections, no Front-Cover Texts, and no Back-Cover Texts.
   A copy of the license is included in the section entitled "GNU
   Free Documentation License".

Timelines
*********

.. contents:: Table of content
    :local:

Concept
=======

From an user point of view
--------------------------

Chill has two objectives : 

* make the administrative tasks more lightweight ;
* help social workers to have all information they need to work

To reach this second objective, Chill provides a special view: **timeline**. On a timeline view, information is gathered and shown on a single page, from the most recent event to the oldest one.

The information gathered is linked to a *context*. This *context* may be, for instance : 

* a person : events linked to this person are shown on the page ;
* a center: events linked to a center are shown. They may concern different peoples ;
* ... 

In other word, the *context* is the kind of argument that will be used in the event's query.

Let us recall that only the data the user has allowed to see should be shown.

.. seealso:: 

   `The issue where the subject was first discussed <https://redmine.champs-libres.coop/issues/224>`_


For developers
--------------

The `Main` bundle provides interfaces and services to help to build timelines. 

If a bundle wants to *push* information in a timeline, it should be create a service which implements `Chill\MainBundle\Timeline\TimelineProviderInterface`, and tag is with `chill.timeline` and arguments defining the supported context (you may use multiple `chill.timeline` tags in order to support multiple context with a single service/class).

If a bundle wants to provide a new context for a timeline, the service `chill.main.timeline_builder` will helps to gather timeline's services supporting the defined context, and run queries across the models. 

.. _understanding-queries :

Understanding queries
^^^^^^^^^^^^^^^^^^^^^

Due to the fact that timelines should show only the X last events from Y differents tables, queries for a timeline may consume a lot of resources: at first on the database, and then on the ORM part, which will have to deserialize DB data to PHP classes, which may not be used if they are not part of the "last X events". 

To avoid such load on database, the objects are queried in two steps : 

1. An UNION request which gather the last X events, ordered by date. The data retrieved are the ID, the date, and a string key: a type. This type discriminates the data type.
2. The PHP objects are queried by ID, the type helps the program to link id with the kind of objects. 

Those methods should ensure that only X PHP objects will be gathered and build by the ORM.

What does the master timeline builder service ?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

 When the service `chill.main.timeline_builder` is instanciated, the service is informed of each service taggued with `chill.timeline` tags. Then, 

1. The service build an UNION query by assembling column and tables names provided by the `fetchQuery` result ;
2. The UNION query is run, the result contains an id and a type for each row (see :ref:`above <understanding-queries>`)
3. The master service gather all id with the same type. Then he searches for the `chill.timeline`'s service which will be able to get the entities. Then, the entities will be fetched using the `fetchEntities` function. All entities are gathered in one query ;
4. The information to render entities in HTML is gathered by passing entity, one by one, on `getEntityTemplate` function.

Pushing events to a timeline
=============================

To push events on a timeline :

1. Create a class which implements `Chill\MainBundle\Timeline\TimelineProviderInterface` ;
2. Define the class as a service, and tag the service with `chill.timeline`, and define the context associated with this timeline (you may add multiple tags for different contexts).

Implementing the TimelineProviderInterface
------------------------------------------

The has the following signature : 

.. code-block:: php

    namespace Chill\MainBundle\Timeline;

    interface TimelineProviderInterface
    {
        
        /** 
         * 
         * @param string $context
         * @param mixed[] $args the argument to the context.
         * @return string[]
         * @throw  \LogicException if the context is not supported
         */
        public function fetchQuery($context, array $args);
        
        /**
         * Indicate if the result type may be handled by the service
         * 
         * @param string $type the key present in the SELECT query
         * @return boolean
         */
        public function supportsType($type);

        /**
         * fetch entities from db into an associative array. The keys **MUST BE**
         * the id
         * 
         * All ids returned by all SELECT queries 
         * (@see TimeLineProviderInterface::fetchQuery) and with the type
         * supported by the provider (@see TimelineProviderInterface::supportsType)
         * will be passed as argument.
         * 
         * @param array $ids an array of id
         * @return mixed[] an associative array of entities, with id as key
         */
        public function getEntities(array $ids);
        
        /**
         * return an associative array with argument to render the entity
         * in an html template, which will be included in the timeline page
         * 
         * The result must have the following key :
         * 
         * - `template` : the template FQDN
         * - `template_data`: the data required by the template
         * 
         * 
         * Example:
         * 
         * ```
         * array( 
         *    'template'      => 'ChillMyBundle:timeline:template.html.twig',
         *    'template_data' => array(
         *             'accompanyingPeriod' => $entity, 
         *              'person' => $args['person'] 
         *         )
         *    );
         * ```
         * 
         * `$context` and `$args` are defined by the bundle which will call the timeline
         * rendering. 
         * 
         * @param type $entity
         * @param type $context
         * @param array $args
         * @return mixed[]
         * @throws \LogicException if the context is not supported
         */
        public function getEntityTemplate($entity, $context, array $args);
     
    }


The `fetchQuery` function
^^^^^^^^^^^^^^^^^^^^^^^^^

The fetchQuery function help to build the UNION query to gather events. This function should return an associative array MUST have the following key :
* `id` : the name of the id column
* `type`: a string to indicate the type
* `date`: the name of the datetime column, used to order entities by date
* `FROM` (in capital) : the FROM clause. May contains JOIN instructions

Those key are optional:
* `WHERE` (in capital) : the WHERE clause. 

 Where relevant, the data must be quoted to avoid SQL injection.

`$context` and `$args` are defined by the bundle which will call the timeline rendering. You may use them to build a different query depending on this context.

For instance, if the context is `'person'`, the args will be this array : 

.. code-block:: php

    array(
        'person' => $person //a \Chill\PersonBundle\Entity\Person entity
    );

You should find in the bundle documentation which contexts are arguments the bundle defines.

.. note::

    We encourage to use `ClassMetaData` to define column names arguments. If you change your column names, changes will be reflected automatically during the execution of your code. 

Example of an implementation :

.. code-block:: php

    namespace Chill\ReportBundle\Timeline;

    use Chill\MainBundle\Timeline\TimelineProviderInterface;
    use Doctrine\ORM\EntityManager;

    /**
     * Provide report for inclusion in timeline
     *
     * @author Julien Fastré <julien.fastre@champs-libres.coop>
     * @author Champs Libres <info@champs-libres.coop>
     */
    class TimelineReportProvider implements TimelineProviderInterface
    {
        
        /**
         *
         * @var EntityManager
         */
        protected $em;
        
        public function __construct(EntityManager $em)
        {
            $this->em = $em;
        }

        public function fetchQuery($context, array $args)
            {
                $this->checkContext($context);
                
                $metadata = $this->em->getClassMetadata('ChillReportBundle:Report');
                
                return array(
                   'id' => $metadata->getColumnName('id'),
                   'type' => 'report',
                   'date' => $metadata->getColumnName('date'),
                   'FROM' => $metadata->getTableName(),
                   'WHERE' => sprintf('%s = %d',
                         $metadata
                            ->getAssociationMapping('person')['joinColumns'][0]['name'],
                         $args['person']->getId())
                );
            }

        //....


    }

The `supportsType` function
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This function indicate to the master `chill.main.timeline_builder` service (which orchestrate the build of UNION queries) that the service supports the type indicated in the result's array of the `fetchQuery` function. 

The implementation of our previous example will be : 

.. code-block:: php 


    namespace Chill\ReportBundle\Timeline;

    use Chill\MainBundle\Timeline\TimelineProviderInterface;
    use Doctrine\ORM\EntityManager;

    class TimelineReportProvider implements TimelineProviderInterface
    {

        //...

        /**
         * 
         * {@inheritDoc}
         */
        public function supportsType($type)
        {
            return $type === 'report';
        }

        //...
    }

The `getEntities` function
^^^^^^^^^^^^^^^^^^^^^^^^^^^

This is where the service must fetch entities from database and return them to the master service.

The results **must be** an array where the id given by the UNION query (remember `fetchQuery`).

.. code-block:: php

    namespace Chill\ReportBundle\Timeline;

    use Chill\MainBundle\Timeline\TimelineProviderInterface;
    use Doctrine\ORM\EntityManager;

    class TimelineReportProvider implements TimelineProviderInterface
    {

        public function getEntities(array $ids)
        {
            $reports = $this->em->getRepository('ChillReportBundle:Report')
                  ->findBy(array('id' => $ids));
            
            $result = array();
            foreach($reports as $report) {
                $result[$report->getId()] = $report;
            }
            
            return $result;
        }

    }

The `getEntityTemplate` function
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This is where the master service will collect information to render the entity. 

The result must be an associative array with : 

- **template** is the FQDN of the template ;
- **template_data** is an associative array where keys are the variables'names for this template, and values are the values.

Example :

.. code-block:: php

    array(
        'template' => 'ChillMyBundle:timeline:template.html.twig',
        'template_data' => array(
            'period' => $entity, 
            'person' => $args['person'] 
            )
        );

The template must, obviously, exists. Example :

.. code-block:: jinja

    <p><i class="fa fa-folder-open"></i>&nbsp;{{ 'An accompanying period is opened for %person% on %date%'|trans({'%person%': person, '%date%': period.dateOpening|localizeddate('long', 'none') } ) }}</p>


Create a timeline with his own context
======================================

You have to create a Controller which will execute the service `chill.main.timeline_builder`. Using the `Chill\MainBundle\Timeline\TimelineBuilder::getTimelineHTML` function, you will get an HTML representation of the timeline, which you may include with twig `raw` filter.

Example : 

.. code-block:: php

    namespace Chill\PersonBundle\Controller;

    use Symfony\Component\HttpFoundation\Response;
    use Symfony\Component\HttpFoundation\Request;
    use Symfony\Bundle\FrameworkBundle\Controller\Controller;

    class TimelinePersonController extends Controller
    {

        public function personAction(Request $request, $person_id)
        {
            $person = $this->getDoctrine()
                    ->getRepository('ChillPersonBundle:Person')
                    ->find($person_id);

            if ($person === NULL) {
                throw $this->createNotFoundException();
            }

            return $this->render('ChillPersonBundle:Timeline:index.html.twig', array
                (
                    'timeline' => $this->get('chill.main.timeline_builder')
                            ->getTimelineHTML('person', array('person' => $person)),
                    'person' => $person
                )
            );
        }

    }
