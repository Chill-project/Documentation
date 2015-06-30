.. Copyright (C)  2014 Champs Libres Cooperative SCRLFS
   Permission is granted to copy, distribute and/or modify this document
   under the terms of the GNU Free Documentation License, Version 1.3
   or any later version published by the Free Software Foundation;
   with no Invariant Sections, no Front-Cover Texts, and no Back-Cover Texts.
   A copy of the license is included in the section entitled "GNU
   Free Documentation License".

.. _install-postgres-server:

Install PostgresSql server
##########################

.. todo::

   the section "Install PostresSql database" must be written. Help appreciated :-)
   
.. note:: 

   To avoid installation and configuration of a postgresql server, you may use `our docker image <https://registry.hub.docker.com/u/chill/database/>`_ to start and configure a database as decribed in the basic installation chapter.
   This solution can be used also in a production environment.
   
.. note::

   Installing unaccent extension on ther server is possible with the package `postgresql-contrib-9.x` (replace 9.x with your server version). The extension may be enabled with running `CREATE EXTENSION unaccent;` in the database, with a superuser account.
   
