This is a fork the [edX-platform](https://github.com/edx/edx-platform) for Trinity University.

See [code.edx.org](http://code.edx.org/) for other parts of the edX code base.

# Installation
Please refer to the edx wiki pages in the [configuration repo](https://github.com/edx/configuration) or [edX-platform](https://github.com/edx/edx-platform) if the instructions below were insufficient or produced errors.

## Dev Stack
These instructions are for developers who want to contribute or make changes to the Trinity University edX source code. The instructions have been modified to be used with Trinity University's forks, the original instructions can be found at [edx Developer Stack](https://github.com/edx/configuration/wiki/edX-Developer-Stack)

* Install [VirtualBox >= 4.2.12](https://www.virtualbox.org/wiki/Downloads) 
* Install [Vagrant >= 1.3.4](https://github.com/edx/configuration/wiki/Installing-Vagrant)
* If running Ubuntu:
    * `sudo apt-get install nfs-common nfs-kernel-server`
* Download the Vagrantfile and create the Vagrant instance:

	```
    mkdir devstack
    cd devstack
    wget https://raw.github.com/trinityuniversity/configuration/master/vagrant/release/devstack/Vagrantfile
    vagrant up
    ```

* Once the VM is created, login to the VM via the command `vagrant ssh`
* Add a line to your development machine's `/etc/hosts` file
    
    ```
    192.168.33.10  preview.localhost
    ```

## Production Stack
Same instructions as the dev stack but download the fullstack VagrantFile instead. Original instructions can be found here [edX Production Stack](https://github.com/edx/configuration/wiki/edX-Production-Stack).

    ```
    wget https://raw.github.com/trinityuniversity/configuration/master/vagrant/release/fullstack/Vagrantfile
    ```

## Issues/Workarounds
Click [here](https://github.com/edx/configuration/wiki/edX-Developer-Stack#issues--workarounds) for issues/workarounds

## Usage
Click [here](https://github.com/edx/configuration/wiki/edX-Developer-Stack#wiki-using-the-edx-devstack) for edx's instructions on using the devstack

# Deployment
## Deploy on Ubuntu Server 12.04 LTS
Original instructions: [edX Ubuntu 12.04 installation](https://github.com/edx/configuration/wiki/edX-Ubuntu-12.04-Installation)

__WARNING__: These instructions will potentially destroy the server they are run on, you should only do them on a virtual machine.

Launch an Ubuntu 12.04 server and login to it as a user that has full sudo privileges. If you use EC2, use at least m1.large type with EBS and appropriate ~50 GB to the root user.

Update your Ubuntu package sources

    sudo apt-get update -y
    sudo apt-get upgrade -y
    reboot

Perform the steps below
    
    sudo apt-get install -y build-essential software-properties-common python-software-properties curl git-core libxml2-dev libxslt1-dev python-pip python-apt python-dev
    wget https://bitbucket.org/pypa/setuptools/raw/0.8/ez_setup.py -O - | sudo python
    sudo pip install --upgrade pip
    sudo pip install --upgrade virtualenv

On the new server, clone the configuration repo:

    cd /var/tmp
    git clone https://github.com/TrinityUniversity/edx-configuration

Install the ansible requirements

    cd /var/tmp/edx-configuration
    sudo pip install -r requirements.txt

Run the edx_sandbox.yml playbook in the configuration/playbooks directory 

    cd /var/tmp/edx-configuration/playbooks 
    sudo ansible-playbook -c local ./edx_sandbox.yml -i "localhost,"

## Update
To update the instance, first bring the `master` branch up to date then:
    
    cd /var/tmp/edx-configuration/playbooks 
    sudo ansible-playbook -c local ./edx_sandbox.yml -i "localhost,"
