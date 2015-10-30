.. Copyright (C)  2014 Champs Libres Cooperative SCRLFS
   Permission is granted to copy, distribute and/or modify this document
   under the terms of the GNU Free Documentation License, Version 1.3
   or any later version published by the Free Software Foundation;
   with no Invariant Sections, no Front-Cover Texts, and no Back-Cover Texts.
   A copy of the license is included in the section entitled "GNU
   Free Documentation License".

Very quick start with docker
#############################


.. _quick-start-with-docker:

We have created a `docker container <https://hub.docker.com/r/chill/demo-flavor/>`_ to let you test `Chill` easily.

.. note:: 

   We assume docker is already installed on your machine. If Docker is not installed, have a look at `the install page in the docker documentation <https://docs.docker.com/>`_.
   
Starting the containers
========================

Mac OS X & docker
-----------------

To use docker on Mac OS X you need to use `boot2docker` or `docker-machine`

Configuration of `boot2docker`
``````````````````````````````

.. code-block:: bash

   $ boot2docker start
   $ boot2docker shellinit


Configuration of `docker-machine`
`````````````````````````````````

.. code-block:: bash

   $ docker-machine start default
   $ eval "$(docker-machine env default)"


Getting the last version of the docker images
----------------------------------------------

.. code-block:: bash

   docker pull chill/database
   docker pull chill/demo-flavor
   
   
Prepare a database
------------------

.. code-block:: bash

   docker run --rm --name=chill_database chill/database
   
The first time you will run this command, the image will be downloaded from docker registry. Please be patient.

Run the container containing the code and attach it to the database
-------------------------------------------------------------------

.. code-block:: bash

   docker run --rm --link chill_database:db -p 8989:8000 --name=chill_php chill/demo-flavor

The image will also be downloaded from docker registry on first run.

You can then browse on `http://localhost:8989 <http://localhost:8989>`_ and login with the created users, like `center a_social` (the complete list is below). Password is always 'password'. For Mac OS X, replace `localhost` by the IP of the docker VM (`boot2docker ip` or `docker-machine ip default`).

Stopping the containers
=======================

.. code-block:: bash

   docker stop chill_php
   docker stop chill_database

Limitations
============

* Those container should not be used in production
* The database should not be persisted or store persistant information: at each container startup, the container's data will be erased and replaced by new (partially) random fixtures

Users created
==============

The passwords are always `password` :

The user's login created are :

* center a_social
* center a_administrative
* center a_direction
* center b_social
* center b_administrative
* center b_direction
* multi_center
* admin



.. _the composer documentation: https://getcomposer.org/doc/
.. _the composer introduction: https://getcomposer.org/doc/00-intro.md
.. _the standard architecture: https://github.com/Champs-Libres/chill-standard
.. _composer: https://getcomposer.org
.. _Firefox: https://www.mozilla.org
.. _symfony framework: http://symfony.com
.. _*unaccent* extension: http://www.postgresql.org/docs/current/static/unaccent.html
