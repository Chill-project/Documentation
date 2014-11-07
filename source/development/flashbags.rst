.. Copyright (C)  2014 Champs Libres Cooperative SCRLFS
   Permission is granted to copy, distribute and/or modify this document
   under the terms of the GNU Free Documentation License, Version 1.3
   or any later version published by the Free Software Foundation;
   with no Invariant Sections, no Front-Cover Texts, and no Back-Cover Texts.
   A copy of the license is included in the section entitled "GNU
   Free Documentation License".

.. _flashbags :

Flashbags: message to users
****************************

The four following levels are defined : 

+-----------+----------------------------------------------------------------------------------------------+
|Key        |Intent                                                                                        |
+===========+==============================================================================================+
|alert      |A message not linked with the user action, but which should require an action or a            |
|           |correction.                                                                                   |
+-----------+----------------------------------------------------------------------------------------------+
|success    |The user action succeeds.                                                                     |
+-----------+----------------------------------------------------------------------------------------------+
|notice     |A simple message to give information to the user. The message may be linked or not linked with|
|           |the user action.                                                                              |
+-----------+----------------------------------------------------------------------------------------------+
|warning    |A message linked with an action, the user should correct.                                     |
+-----------+----------------------------------------------------------------------------------------------+
|error      |The user's action failed: he must correct something to process the action.                    |
+-----------+----------------------------------------------------------------------------------------------+

.. seealso::

   `Flash Messages on Symfony documentation <http://symfony.com/doc/current/book/controller.html#flash-messages>`_
      Learn how to use flash messages in controller.
