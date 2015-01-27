.. Copyright (C)  2014 Champs Libres Cooperative SCRLFS
   Permission is granted to copy, distribute and/or modify this document
   under the terms of the GNU Free Documentation License, Version 1.3
   or any later version published by the Free Software Foundation;
   with no Invariant Sections, no Front-Cover Texts, and no Back-Cover Texts.
   A copy of the license is included in the section entitled "GNU
   Free Documentation License".

Localisation
*************

Language in url
===============

Language should be present in URL, conventionnaly as first argument. 

.. code-block:: none

   /fr/your/url/here

This allow users to change from one language to another one on each page, which may be useful in multilanguages teams. If the installation is single-language, the language switcher will not appears.

This is an example of routing defined in yaml : 

.. code-block:: yaml

   chill_person_general_edit:
       pattern: /{_locale}/person/{person_id}/general/edit
       defaults: {_controller: ChillPersonBundle:Person:edit }


Date and time
==============

The `Intl extension <http://twig.sensiolabs.org/doc/extensions/intl.html>`_ is enabled on the Chill application. 

You may format date and time using the `localizeddate` function : 

.. code-block:: jinja

   date|localizeddate('long', 'none')

By default, we prefer using the `long` format for date formatting.

.. seealso::

   `Documentation for Intl Extension <http://twig.sensiolabs.org/doc/extensions/intl.html>`_
      Read the complete doc for the Intl extension.
   
