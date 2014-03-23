********
Devstack
********

Installation
============
Prerequisites
-------------

* `VirtualBox <https://www.virtualbox.org/>`_ (4.2.12 or newer)
* `Vagrant <http://www.vagrantup.com/>`_ (1.4 or newer)

Instructions
------------
Now that we have virtualbox and vagrant we can start working with platform. Create a working directory to store the project files::

    mkdir devstack
    cd devstack

First we want to clone Trinityx so that our new development machine will use our fork instead of edx's code::

    git clone https://github.com/TrinityUniversity/edx-platform.git

Now we will download the Vagrantfile and start the virtual machine (via Vagrant). For more information on what is occuring during this step please visit the `vagrant documentation <http://docs.vagrantup.com/>`_ ::
    
    wget https://raw.github.com/TrinityUniversity/edx-configuration/master/vagrant/release/devstack/Vagrantfile
    vagrant up --no-provision

.. note::
    
    Vagrant will provision your vm the first time you run `vagrant up`. To use Trinityx's fork, we need to ensure that this does not happen by using the `--no-provision` flag.    

This will create a virtual machine (guest) but it won't be provisioned for the edx platform yet. Any running of code or testing should be run inside of the guest. To access the guest run::

    vagrant ssh

We now need to modify the virtual machine so that it utilizes Trinity's configuration code. To do this we'll ssh into the machine and swap out the default configuration files with Trinityx's configuration repo::

    vagrant ssh
    sudo rm /edx/app/edx_ansible/edx_ansible/
    git clone https://github.com/TrinityUniversity/edx-configuration /edx/app/edx_ansible/edx_ansible

We are now ready to provision our machine (from the host)::
    
    vagrant provision

Enable Preview
--------------

To enable "preview" in studio you'll need to add the following line to your hosts (linux/mac - /etc/hosts, windows - C:\Windows\System32\drivers\etc\hosts)::

    192.168.33.10  preview.localhost`

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

Updating
========
If you ever need to update the configuration or platform files from within the gues then the following commands are available::

    sudo /edx/bin/update edx-platform master
    sudo /edx/bin/update configuration master

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
If you see this error when attempting to run the server then you need to remove the lock and restart mongodb. This error usually occurs when the VM is not shutdown properly. To avoid this problem in the future, use one of the vagrant commands to suspend the VM (e.g. ``vagrant halt`` or ``vagrant suspend``). To fix the problem::
            
            vagrant ssh
            sudo rm /edx/var/mongo/mongodb/mongod.lock
            sudo start mongodb. 

Further troubleshooting
-----------------------
Visit `this link <https://github.com/edx/configuration/wiki/edX-Developer-Stack#wiki-issues--workarounds>`_ for more troubleshooting assistance.
