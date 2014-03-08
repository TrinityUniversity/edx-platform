********
Devstack
********

Installation
============
Prerequisites
-------------

* `VirtualBox <https://www.virtualbox.org/>`_ (4.2.12 or newer)
* `Vagrant <http://www.vagrantup.com/>`_ (1.4 or newer)

Setup Vagrant
-------------
Now that we have virtualbox and vagrant we can start working with platform. Create a working directory to store the project files::

    mkdir devstack
    cd devstack

Download the Vagrantfile and start the virtual machine (via Vagrant). For more information on what is occuring during this step please visit the `vagrant documentation <http://docs.vagrantup.com/>`_ ::

    wget https://raw.github.com/TrinityUniversity/edx-configuration/master/vagrant/release/devstack/Vagrantfile
    vagrant up

This will create a virtual machine (guest) that will be fully provisioned to run the edx-platform. Any running of code or testing should be run inside of the guest. To access the guest run::

    vagrant ssh


Enable Preview
--------------

To enable "preview" in studio you'll need to add the following line to your hosts (linux/mac - /etc/hosts, windows - C:\Windows\System32\drivers\etc\hosts)::

    192.168.33.10  preview.localhost`


Use Trinityx Fork
-----------------

We now need to change the stack to use Trinity's fork of the edx-platform. First we need to remove the old code, I'd recommend doing this in the host OS to avoid annoying problems with the NFS server::
    
    sudo rm -rf /edx/app/edxapp/edx-platform/* 

Then connect to the devstack::

    vagrant ssh

Add the following line to ``/edx/etc/server-vars.yml``::

    edx_platform_repo: "https://github.com/TrinityUniversity/edx-platform.git"

Now we're ready to update the edx-platform to use our forked repo::

    vagrant reload
    vagrant ssh
    sudo /edx/bin/update edx-platform master

.. note::

    During updates the checking out of the code may take a long time.

Usage
=====
By default LMS and Studio are not running. To run them we'll first need to ssh into our guest and then switch to the edxapp user::

    vagrant ssh
    sudo su edxapp

The following handy dandy rake commands will bring up our servers. You'll need to seperate terminals to run each of the servers seperately::

    rake devstack[lms]
    rake devstack[studio]

You'll now be able to access lms(http://127.0.0.1:8000) and studio(http://127.0.0.1:8001) from your browser in the host. Some default users were created for your convenience.

==================== ======== ========
Email                Password Mode
==================== ======== ========
honor@example.com    edx      honor
audit@example.com    edx      audit
verified@example.com edx      verified
==================== ======== ========

Development
===========
During the configuration process, several directories were created inline with your Vagrantfile. These directories are checked out copies of the edx-platform components. Currently only 'edx-platform' is forked by Trinity.

You can treat the edx-platform as any git directory. For a full explanation on how the code is managed, please read the git workflow section(TODO).

To get started, create your own branch of master `git checkout -b master <YOUR_NAME>`. Any changes you make here will be reflected in the edx-platform environment being run in your guest machine. 

Troubleshooting
===============
.. note::
    Most of the troubleshooting commands assume you are using the vagrant account. Press CTRL+D to return from the edxapp user to the vagrant user.

pymongo.errors.ConnectionFailure: could not connect to localhost:27017: [Errno 111] Connection refused
------------------------------------------------------------------------------------------------------
If you see this error when attempting to run the server then you need to remove the lock and restart mongodb. This error usually occurs when the VM is not shutdown properly. To avoid this problem in the future, use one of the vagrant commands to suspend the VM (e.g. ``vagrant halt`` or ``vagrant suspend``). ::
            
            vagrant ssh
            sudo rm /edx/var/mongo/mongodb/mongod.lock
            sudo start mongodb. 

Further troubleshooting
-----------------------
Visit `this link <https://github.com/edx/configuration/wiki/edX-Developer-Stack#wiki-issues--workarounds>`_ for more troubleshooting assistance.
