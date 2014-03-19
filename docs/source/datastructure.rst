*********************
Data-Structure Drawer
*********************

Overview
========
The data-structure drawer (dsd) is an XBlock created by Trinity University for the Edx platform (TrinityX). 

Development
===========
* ``git clone https://github.com/TrinityUniversity/xblock-dsd``
* Install the `xblock-sdk <https://github.com/edx/xblock-sdk>`_ (with virtualenv)
* ``pip install dsd``
* ``cd xblock-sdk``
* ``python manage.py runserver``

Now the dsd XBlock will be available at `127.0.0.1:8000 <http://127.0.0.1:8000>`_. After you've made changes to dsd you'll need to update the virtualenv ``pip install dsd --upgrade`` 

To test the xblock with lms/studio follow the instructions `here <https://github.com/edx/edx-platform/blob/master/docs/en_us/developers/source/xblocks.rst#testing>`_
