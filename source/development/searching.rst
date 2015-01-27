.. Copyright (C)  2014 Champs Libres Cooperative SCRLFS
   Permission is granted to copy, distribute and/or modify this document
   under the terms of the GNU Free Documentation License, Version 1.3
   or any later version published by the Free Software Foundation;
   with no Invariant Sections, no Front-Cover Texts, and no Back-Cover Texts.
   A copy of the license is included in the section entitled "GNU
   Free Documentation License".


Searching
*********

Chill should provide information needed by users when they need it. Searching within bundle, entities,... is an important feature to achieve this goal.

The Main Bundle provide interfaces to ease the developer work. It will also attempt that search will work in the same way accross bundles.

.. contents:: Table of content
   :local:

.. seealso::

   `Our blog post about searching (in French) <http://blog.champs-libres.coop/vie-des-champs/2015/01/06/va-chercher-chill-la-recherche-dans-chill-logiciel-libre-service-social.html>`_
      This blog post give some information for end-users about searching.

   `The issue about search behaviour <https://redmine.champs-libres.coop/issues/377>`_
      Where the search behaviour is defined.

Searching in a glance for developers
====================================

Chill suggests to use an easy-to-learn language search.

.. note::

   We are planning to provide a form to create automatically search pattern according to this language. Watch the `issue regarding this feature <https://redmine.champs-libres.coop/issues/389>`_.

The language is an association of search terms. Search terms may contains : 

- **a domain**: this is "the domain you want to search" : it may some entities like people, reports, ... Example : `@person` to search accross people, `@report` to browse reports, ... The search pattern may have **a maximum of one** domain by search, providing more should throw an error, and trigger a warning for users.
- **arguments and their values** : This is "what you search". Arguments narrow the search to specific fields : username, date of birth, nationality, ... The syntax is `argument:value`. I.e.: ` birthdate:2014-12-15`, `firstname:Depardieu`, ... **Arguments are optional**. If the value of an argument contains spaces or characters like punctuation, quotes ("), the value should be provided between parenthesis : `firstname:(Van de snoeck)`, `firstname:(M'bola)`, ...
- **default value** : this the "rest" of the search, not linked with any arguments or domain. Example : `@person dep` (`dep` is the "default value"), or simply `dep` if any domain is provided (which is perfectly acceptable). If a string is not idenfied as argument or domain, it will be present in the "default" term.

If a search pattern (provided by the user) does not contains any domain, the search must be run across default domain/search modules.

A domain may be supported by different search modules. For instance, if you provide the domain `@person`, the end-user may receive results of exact firstname/lastname, but also result with spelling suggestion, ... **But** if results do not fit into the first page (if you have 75 results and the screen show only 50 results), the next page should contains only the results from the required module. 

For instance : a user search across people by firstname/lastname, the exact spelling contains 10 results, the "spelling suggestion" results contains 75 names, but show only the first 50. If the user want to see the last 25, the next screen should not contains the results by firstname/lastname.

Allowed characters as arguments
===============================

In order to execute regular expression, the allowed chararcters in arguments are a-z characters, numbers, and the sign '-'. Spaces and special characters like accents are note allowed (the accents are removed during parsing).

Special characters and uppercase
================================

The search should not care about lowercase/uppercase and accentued characters. Currently, they are removed automatically by the `chill.main.search_provider`.

Implementing search module for dev
===================================

To implement a search module, you should : 

- create a class which implements the `Chill\MainBundle\Search\SearchInterface` class. An abstract class `Chill\MainBundle\Search\AbstractSearch` will provide useful assertions for parsing date string to `DateTime` objects, ...
- register the class as a service, and tag the service with `chill.search` and an appropriate alias

The search logic is provided under the  `/search` route.

.. seealso::

   `The implementation of a search module in Person bundle <https://github.com/Chill-project/Person/blob/master/Search/PersonSearch.php>`_
      An example of implementationhttps://github.com/Chill-project/Main/blob/master/DependencyInjection/SearchableServicesCompilerPass.php

.. note::

   **Internals explained** : the services tagged with `chill.search` are gathered into the `chill.main.search_provider` service during compilation (`see the compiler pass <https://github.com/Chill-project/Main/blob/master/DependencyInjection/SearchableServicesCompilerPass.php>`_). 

   The `chill.main.search_provider` service allow to : 
  
   - retrieve all results (as html string) for all search module concerned by the search (according to the domain provided or modules marked as default)
   - retrieve result for one search module

The SearchInterface class
-------------------------

.. code-block:: php

   namespace Chill\PersonBundle\Search;

   use Chill\MainBundle\Search\AbstractSearch;
   use Doctrine\ORM\EntityManagerInterface;
   use Chill\PersonBundle\Entity\Person;
   use Symfony\Component\DependencyInjection\ContainerInterface;
   use Symfony\Component\DependencyInjection\ContainerAware;
   use Symfony\Component\DependencyInjection\ContainerAwareTrait;
   use Chill\MainBundle\Search\ParsingException;

   class PersonSearch extends AbstractSearch
   {

       // indicate which domain you support
       // you may respond TRUE to multiple domain, according to your logic
       public function supports($domain)
       {
           return 'person' === $domain;
       }

       // if your domain must be called when no domain is provided, should return true
       public function isActiveByDefault()
       {
           return true;
       }
       
       // if multiple module respond to the same domain, indicate an order for your search.
       public function getOrder()
       {
           return 100;
       }


       // This is where your search logic should be executed. 
       // This method must return an HTML string (a string with HTML tags)
       // see below about the structure of the $term array
       public function renderResult(array $terms, $start = 0, $limit = 50, array $options = array())
       {  
           return $this->container->get('templating')->render('ChillPersonBundle:Person:list.html.twig', 
                   array( 
                       // you should implements the `search` function somewhere :-)
                       'persons' => $this->search($terms, $start, $limit, $options),
                       // recomposePattern is available in AbstractSearch class
                       'pattern' => $this->recomposePattern($terms, array('nationality',
                           'firstname', 'lastname', 'birthdate', 'gender'), $terms['_domain']),
                       // you should implement the `count` function somewhere :-)
                       'total' => $this->count($terms)
                   ));
       }
   }


Structure of array `$term`
^^^^^^^^^^^^^^^^^^^^^^^^^^

The array term is parsed automatically by the `main.chill.search_provider` service. 

.. note::
   If you need to parse a search pattern, you may use the function `parse($pattern)` provided by the service.

The array `$term` have the following structure after parsing :

.. code-block:: php

   array(
      '_domain' => 'person', //the domain, without the '@'
      'argument1' => 'value', //the argument1, with his value
      'argument2' => 'my value with spaces', //the argument2
      '_default' => 'abcde ef' // the default term
   );

The original search would have been : `@person argument1:value argument2:(my value with spaces) abcde ef`

.. warning::
   The search values are always unaccented.

Register the service
--------------------

You should add your service in the configuration, and add a `chill.search` tag and an alias.

Example : 

.. code-block:: yaml

   services:
      chill.person.search_person:
         class: Chill\PersonBundle\Search\PersonSearch
         #your logic here
         tags:
            - { name: chill.search, alias: 'person_regular' }

The alias will be used to get the results narrowed to this search module, in case of pagination (see above).

Parsing date
============

The class `Chill\MainBundle\Search\AbstractSearch` provides a method to parse date :

.. code-block:: php

   //from subclasses
   $date = $this->parseDate($string);

`$date` will be an instance of `DateTime <http://php.net/manual/en/class.datetime.php>`_.

.. seealso::

   `The possibility to add periods instead of date <https://redmine.champs-libres.coop/issues/390>`_
       Which may be a future improvement for search with date.

Exceptions
==========

The logic of the search is handled by the controller for the `/search` path. 

You should throw those Exception from your instance of `SearchInterface` if needed : 

Chill\MainBundle\Search\ParsingException
   If the terms does not fit your search logic (for instance, conflicting terms)

Expected behaviour
==================

Operators between multiple terms
--------------------------------

Multiple terms should be considered are "AND" instructions :

@person nationality:RU firstname:dep 
   the people having the Russian nationality AND having DEP in their name

@person birthdate:2015-12-12 charles 
   the people having 'charles' in their name or firstname AND born on December 12 2015

Spaces in default
-----------------

Spaces in default terms should be considered as "AND" instruction

@person charle dep 
   people having "dep" AND "charles" in their firstname or lastname. Match "Charles Depardieu" but not "Gérard Depardieu" ('charle' is not present)

Rendering
---------

The rendering should contains :

- the total number of results ;
- the search pattern in the search language. The aim of this is to let users learn the search language easily.
- a title

Frequently Asked Questions (FAQ)
================================

Why renderResults returns an HTML string and not structured array ?
   It seems that the form of results may vary (according to access-right logic, ...) and is not easily structurable






