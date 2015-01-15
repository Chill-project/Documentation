.. Copyright (C)  2014 Champs Libres Cooperative SCRLFS
   Permission is granted to copy, distribute and/or modify this document
   under the terms of the GNU Free Documentation License, Version 1.3
   or any later version published by the Free Software Foundation;
   with no Invariant Sections, no Front-Cover Texts, and no Back-Cover Texts.
   A copy of the license is included in the section entitled "GNU
   Free Documentation License".
   
Person bundle
###########

This bundle provides the ability to record people in the software. This bundle is required by other bundle

Entities provided
*****************

.. todo::

   describe entities provided by person bundle
   
   
Search terms
*************

The class `Chill\PersonBundle\Search\PersonSearch` provide the search module.

Domain
=======

The search upon "person" is provided by default. The `@person` domain search may be omitted.

* `@person` is the domain search for people.


Arguments
=========

* `firstname` : provide the search on firstname. Example : `firstname:Depardieu`. May match part of the firstname (`firsname:dep` will match **Dep**ardieu)
* `lastname` : provide the search on lastname. May match part of the lastname.
* `birthdate` : provide the search on the birthdate. Example : `birthdate:1996-01-19`
* `gender`: performs search on man/woman. The accepted values are `man` or `woman`.
* `nationality` : performs search on nationality. Value must be a country code `as described in ISO 3166 <http://www.iso.org/iso/fr/home/standards/country_codes.htm>`_. Example : `nationality:FR`.

Default
=======

The default search is performed on firstname and/or lastname. Both are concatened before search. If values are separated by spaces, the clause `AND` is used : the search `dep ge` will match '**Gé**rard **Dep**ardieu` or 'Jean **Dep**a**ge**lles', but not 'Charline **Dep**ardieu' (missing 'Ge' in word).
