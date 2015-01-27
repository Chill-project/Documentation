.. Copyright (C)  2014 Champs Libres Cooperative SCRLFS
   Permission is granted to copy, distribute and/or modify this document
   under the terms of the GNU Free Documentation License, Version 1.3
   or any later version published by the Free Software Foundation;
   with no Invariant Sections, no Front-Cover Texts, and no Back-Cover Texts.
   A copy of the license is included in the section entitled "GNU
   Free Documentation License".
   
Report bundle
#############

This bundle provides the ability to record report about people. We use custom fields to let user add fields to reports.

.. contents:: Table of content
   :local:

.. todo::

   The documentation about report is not writtend

Concepts
========


Search
======

Domain
------

* `@report` is the domain search for reports.


Arguments
---------

* `date` : The date of the report

Default
-------

The report's date is the default value. 

An error is thrown if an argument `date` and a default is used.
