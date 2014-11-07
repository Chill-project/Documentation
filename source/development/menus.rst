.. Copyright (C)  2014 Champs Libres Cooperative SCRLFS
   Permission is granted to copy, distribute and/or modify this document
   under the terms of the GNU Free Documentation License, Version 1.3
   or any later version published by the Free Software Foundation;
   with no Invariant Sections, no Front-Cover Texts, and no Back-Cover Texts.
   A copy of the license is included in the section entitled "GNU
   Free Documentation License".

.. _menus :

Menus
*****

Chill has created his own menu system

.. seealso::

   `Routes dans Chill [specification] <https://redmine.champs-libres.coop/issues/179>`_
       The issue wich discussed the implementation of routes.

Concepts
========

.. warning:: 

   to be written



Add a menu in a template
========================

In your twig template, use the `chill_menu` function :

.. code-block:: html+jinja

   {{ chill_menu('person', {
	     'layout': 'ChillPersonBundle::menu.html.twig',
	     'args' : {'id': person.id },
	     'activeRouteKey': 'chill_person_view'
	}) }}

The available arguments are:

* `layout` : a custom layout. Default to `ChillMainBundle:Menu:defaultMenu.html.twig`
* `args` : those arguments will be passed through the url generator.
* `activeRouteKey` must be the route key name.

.. note:: 

   The argument `activeRouteKey` may be a twig variable, defined elsewhere in your template, even in child templates.



Create an entry in an existing menu
===================================

If a route belongs to a menu, you simply add this to his definition in routing.yml :

.. code-block:: yaml

   chill_person_history_list:
       pattern: /person/{person_id}/history
       defaults: { _controller: ChillPersonBundle:History:list }
       options:
           #declare menus
           menus: 
               # the route should be in 'person' menu : 
               person:
                   #and have those arguments :
                   order: 100
                   label: menu.person.history

* `order` (mandatory) : the order in the menu. It is preferrable to increment by far more than 1.
* `label` (mandatory) : a translatable string. 
* `helper` (optional) : a text to help people to understand what does the menu do. Not used in default implementation.
* `condition` (optional) : an `Expression Language <http://symfony.com/doc/current/components/expression_language/index.html> `_ which will make the menu appears or not. Typically, it may be used to say "show this menu only if the person concerned is more than 18". **Not implemented yet**.
* `access` (optional) : an Expression Language to evalute the possibility, for the user, to show this menu according to Access Control Model. **Not implemented yet.**

You may add additional keys, but should not use the keys described above.

You may add the same route to multiple menus :

.. code-block:: yaml

   chill_person_history_list:
       pattern: /person/{person_id}/history
       defaults: { _controller: ChillPersonBundle:History:list }
       options:
           menus: 
               menu1:
                   order: 100
                   label: menu.person.history
               menu2:
                   order: 100
                   label: another.label



Customize menu rendering
========================

You may customize menu rendering by using the `layout` option.

.. warning ::
   
   TODO : this part should be written.






.. _caveats :

Caveats
=======

Currently, you may pass arguments globally to each menu, and they will be all passed to route url. This means that :

* the argument name in the route entry must match the argument key in menu declaration in twig template
* if an argument is missing to generate an url, the url generator will throw an error
* if the argument name is not declared in route entry, it will be added to the url, (example: `/my/route?additional=foo`)
