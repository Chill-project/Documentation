.. Copyright (C)  2014 Champs Libres Cooperative SCRLFS
   Permission is granted to copy, distribute and/or modify this document
   under the terms of the GNU Free Documentation License, Version 1.3
   or any later version published by the Free Software Foundation;
   with no Invariant Sections, no Front-Cover Texts, and no Back-Cover Texts.
   A copy of the license is included in the section entitled "GNU
   Free Documentation License".

Routing and menus
*****************


The _Chill_'s architecture allows to choose bundle on each installation. This may lead to a huge diversity of installations, and a the developper challenge is to make his code working with all those possibles installations.

_Chill_ uses menus to let users access easily to the most used functionalities. For instance, when you land on a "Person" page, you may access directly to his activities, notes, documents, ... in a single click on a side menu.

For a developer, it is easy to extend this menu with his own entries.

.. seealso::

   `Symfony documentation about routing <http://symfony.com/doc/current/book/routing.html>`_
      This documentation should be read before diving into those lines

   `Routes dans Chill <https://redmine.champs-libres.coop/issues/179>`_ (FR)
      The issue where we discussed routes. In French.

Create routes 
==============

.. note::

   We recommand using `yaml` to define routes. We have not tested the existing other ways to create routes (annotations, ...). Help wanted.

The first step is as easy as create a route in symfony, and add some options in his description :

.. code-block:: yaml

   chill_main_dummy_0:
       pattern: /dummy/{personId}
       defaults: { _controller: CLChillMainBundle:Default:index }
       options:
           #we begin menu information here :
           menus: 
               foo: #must appears in menu named 'foo'
                   order: 500  #the order will be '500'
                   label: foolabel #the label shown on menu. Will be translated
                   otherkey: othervalue #you may add other informations, as needed by your layout
               bar: #must also appears in menu named 'bar'
                   order: 500
                   label: barlabel

The mandatory parameters under the `menus` definition are : 

* `name`: the menu's name, defined as an key for the following entries
* `order`. Note: if we have duplicate order's values, the order will be incremented. We recommand using big intervals within orders and publishing the orders in your documentation
* `label`: the text which will be rendered inside the `<a>` tag. The label should be processed trough the `trans` filter (`{{ route.label|trans }}`)

You *may* also add other keys, which will be used optionally in the way the menu is rendered. See 

.. warning::

   Although all keys will be kept from your `yaml` definition to your menu template, we recommend not using those keys, which are reserved for a future implementations of Chill : 

   * `helper`, a text to help user or add more informations to him
   * `access` : which will run a test with `Expression Langage <http://symfony.com/doc/current/components/expression_language/index.html>`_ to determine if the user has the ACL to show the menu entry ;
   * `condition`, which will test with the menu context if the entry must appears

Show menu in twig templates
===========================

To show our previous menu in the twig template, we invoke the `chill_menu` function. This will render the `foo` menu :

.. code-block:: jinja

   {{ chill_menu('foo') }}

Passing variables
^^^^^^^^^^^^^^^^^

If your routes need arguments, i.e. an entity id, you should pass the as argument to the chill_menu function. If your route's pattern is `/person/{personId}`, your code become :

.. code-block:: jinja

   {{ chill_menu('foo', { 'args' : { 'personId' : person.id } } ) }}

Of course, `person` is a variable you must define in your code, which should have an `id` accessible property (i.e. : `$person->getId()`).

.. note::

   Be aware that your arguments will be passed to all routes in a menu. If a route does not require `personId` in his pattern, the route will become `/pattern?personId=XYZ`. This should not cause problem in your application.

.. warning::

   It is a good idea to reuse the same parameter's name in your pattern, to avoid collision. Prefer `/person/{personId}` to `/person/{id}`. 

   If you don't do that and another developer create a bundle with  `person/{personId}/{id}` where `{id}` is the key for something else, this will cause a lot of trouble...

Rendering active entry
^^^^^^^^^^^^^^^^^^^^^^

Now, you want to render differently the *active* route of the menu [#f1]_. You should, in your controller or template, add the active route in your menu : 

.. code-block:: jinja

   {{ chill_menu('foo', { 'activeRouteKey' : 'chill_main_dummy_0' } ) }}

On menu creation, the route wich has the key `chill_main_dummy_0` will be rendered on a different manner.

Define your own template
-------------------------

By default, the menu is rendered with the default template, which is a simple `ul` list. You may create your own templates :

.. code-block:: html+jinja

   #MyBundle/Resources/views/Menu/MyMenu.html.twig
   <ul class="myMenu">
   {% for route in routes %}
       <li><a href="{{ path(route.key, args ) }}" class="{%- if activeRouteKey == route.key -%}active{%- endif -%}">{{ route.label|trans }}</a></li>
   {% endfor %}
   </ul>

Arguments available in your template :

* The `args` value are the value passed in the 'args' arguments requested by the `chill_menu` function.
* `activeRouteKey` is the key of the currently active route. 
* `routes` is an array of routes. The array has this structure: `routes[order] = { 'key' : 'the_route_key', 'label' : 'the route label' }` The order is *resolved*: in case of collision (two routes from different bundles having the same order), the order will be incremented. You may find in the array your own keys (`{ 'otherkey' : 'othervalue'}` in the example above).

Then, you will call your own template with the `layout` argument :

.. code-block:: jinja

   {{ chill_menu('foo', { 'layout' : 'MyBundle:Menu:MyMenu.html.twig' } ) }}

.. note::

   Take care of specifying the absolute path to layout in the function.



.. rubric:: Footnotes

.. [#f1] In the default template, the currently active entry will be rendered with an "active" class : `<li class="active"> ... </li>`
