.. Copyright (C)  2015 Champs Libres Cooperative SCRLFS
   Permission is granted to copy, distribute and/or modify this document
   under the terms of the GNU Free Documentation License, Version 1.3
   or any later version published by the Free Software Foundation;
   with no Invariant Sections, no Front-Cover Texts, and no Back-Cover Texts.
   A copy of the license is included in the section entitled "GNU
   Free Documentation License".


Layout / Template usage
#######################

We recommand the use of the existing layouts to ensure the consistency of the design. This section explains the different templates and how to use it.

The layouts are twig templates.

Organisation of the layouts
===========================

ChillMainBundle::layout.html.twig
---------------------------------

This is the base layout. It includes the most import css / js files. It display a page with

* a horizontal navigation menu
* a place for content
* a footer


The layout containts blocks, that are :

* title

  * to display title

* css

  * where to add some custom css

* navigation_section_menu

  * place where to insert the section menu in the navigation menu (by default the navigation menu is inserted)

* navigation_search_bar

  * place where to insert a search bar in the navigation menu (by default the search bar is inserted)

* top_banner

  * place where to display a banner below the navigation menu (this place is use to display the details of the person)

* sublayout_containter

  * place between the header and the footer that can be used to create a new layout (with vertical menu for example)

* content

  * place where to display the content (flash message are included outside of this block)

* js

  * where to add some custom javascript


ChillMainBundle::layoutWithVerticalMenu.html.twig
-------------------------------------------------

This layout extends `ChillMainBundle::layout.html.twig`. It replaces the block `layout_content` and divides this block for displaying a vertical menu and some content.

It proposes 2 new blocks :

* layout_wvm_content

  * where to display the page content

* vertical_menu_content

  * where to place the vertical menu


ChillMainBundle::Admin/layout.html.twig
---------------------------------------

This layout extends `ChillMainBundle::layout.html.twig`. It hides the search bar, remplaces the `section menu` with the `admin section menu`.

It proposes a new block :

* admin_content

  * where to display the admin content


ChillMainBundle::Admin/layoutWithVerticalMenu.html.twig
-------------------------------------------------------

This layout extends `ChillMainBundle::layoutWithVerticalMenu.html.twig`. It do the same changes than `ChillMainBundle::Admin/layout.html.twig` : hiding the search bar, remplacing the `section menu` with the `admin section menu`.

It proposes a new block :

* admin_content

  * where to display the admin content

ChillPersonBundle::layout.html.twig
-----------------------------------

This layout extend `ChillMainBundle::layoutWithVerticalMenu.html.twig` add the person details in the block `top_banner`, set the menu `person` as the vertical menu.

It proposes 1 new block :

* personcontent

  * where to display the information of the person


ChillMainBundle::Export/layout.html.twig
----------------------------------------

This layout extends `ChillMainBundle::layoutWithVerticalMenu.html.twig` and set the menu `export` as the vertical menu.

It proposes 1 new block :

* export_content

  * where to display the content of the export