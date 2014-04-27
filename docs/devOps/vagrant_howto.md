
Vagrant HOWTO - TODO
--------------------

This is to assist with setting up a development or testing environment VM on a Linux or OSX system
with vagrant.

## Assumptions

 * Base work directory is at /home/user/siminars - Siminars git modules
 (including deployment) are cloned under this directory.

## Process

 * [Install VirtualBox](https://www.virtualbox.org/wiki/Linux_Downloads)
 - it's recommended that you use the virtualbox.org packages instead of
 the ones provided by your distro, as vagrant seems to be dependent on
 versions. At time of writing I'm using VirtualBox 4.3.x.

 * [Download and install Vagrant](http://www.vagrantup.com/downloads.html)

 * Some plugins will make things better and deal with some bugs. Install as:

    ```bash
    $ sudo vagrant plugin install [pluginname]
    ```

    ..* `vagrant-cachier` - Package cache to reduce downloading between similar VMs.
    ..* `vagrant-vbguest` - fixes an issue with guest support in some ubuntu saucy boxes.

 * Add some boxes:

    ```bash
    $ vagrant box add -n vb-ubusrv-stable http://cloud-images.ubuntu.com/vagrant/saucy/current/saucy-server-cloudimg-amd64-vagrant-disk1.box
    ```

    This will create a new box from the official ubuntu cloud images
    for Saucy Server, and name it vb-ubusrv-stable.

 * Now we want to create a new VM with this box.

    ```bash
    $ cd /home/user/siminars
    $ vagrant init vb-ubusrv-stable
    ```

    This will create a new generic Vagrantfile for your new VM in the
    work directory. Browse this to get an idea of some of the things that
    can be configured here. Some of them will depend on the underlying
    VM engine (we are using virtualbox here, but LXC, KVM etc are also
    available) and some on additional plugins. You don't have to change
    anything to just run the VM right now, though.

 * Let's do a first time run:

    ```bash
    $ vagrant up
    ... bunch of initialization stuff should happen here and VM will start...
    $ vagrant ssh
    ```

At this point you should be logged into your vm as user `vagrant` and
`/home/users/siminars` will be mounted at `/vagrant`.

TODO - Initial provisioning, puppet etc.
