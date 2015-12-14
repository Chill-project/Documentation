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

On a linux environment, installing Postgresql server is very easy.
Here follows the instructions for a debian based distribution (as Ubuntu) using the distribution repositories.
To have the latest version follow the instructions of the `Postgresql wiki`_.

.. code-block:: bash
	
	sudo apt-get update
	sudo apt-get install postgresql   

	
Having a look at the install messages, you will guess quickly which is the version installed.
Anyway you can check this with the following code:

.. code-block:: bash
	
	psql --version
	>>> psql (PostgreSQL) 9.4.5

To be able to add the unaccent extension to your database, 
you will have to install the following package where x.x are the first 2 parts of the version, 
9.4 in this example.

.. code-block:: bash

	sudo apt-get install postgresql-contrib-x.x
	
You are ready to play with Postgressql.
	
.. note:: 

   To avoid installation and configuration of a postgresql server, you may use `our docker image <https://registry.hub.docker.com/u/chill/database/>`_ to start and configure a database as decribed in the basic installation chapter.
   This solution can be used also in a production environment.
      
   
Install PostresSql database
###########################

Here follows as an example the instructions that has been used on Ubuntu 14.04 distribution to install the Chill database.
Feel free to customize it following your preferences, but do not forget to enable the `unaccent` extension on your database.

.. code-block:: bash
  
   sudo su
   su postgres
   # At the prompt of the following instruction, I have typed 'my_terrible_secret' as password
   createuser --pwprompt chill_user
   createdb -O chill_user chill_db
   psql -d chill_db -c "CREATE EXTENSION unaccent;"
	
When you will use composer to install Chill, you will have to provide some database information.
If you follow this tutorial these will be:
   
   - database_host: localhost
   - database_port: 5432
   - database_name: chill_db
   - database_user: chill_user
   - database_password: my_terrible_secret
   - locale: en
   
 
 .. _Postgresql wiki: https://wiki.postgresql.org/wiki/Apt