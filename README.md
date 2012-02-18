Install chef-server from scratch
================================

Installing your own Chef server can be a bit hoop-jumpy. This abstracts all of those hoops into one (ugly) script.

What does it do?
----------------

Chef server depends on a truckload of not-necessarily-easy-to-install things. The script:

+ Installs RVM
+ Installs Ruby 1.9.2 (which isn't yet in the Ubuntu repos)
+ Installs CouchDB
+ Installs RabbitMQ
+ Installs Sun Java 1.6 (using [this witchcraft](https://github.com/flexiondotorg/oab-java6))
+ Installs OpsCode's Gecode
+ Installs the Chef gems (with rvmsudo, which is awesome)
+ Configures the Chef server
+ Creates upstart scripts for each of the Chef components

How to use it
-------------

You'll probably want to create a user to run all of this (I used 'chef'). This user needs to be able to sudo root - the shortest path to happiness (on Ubuntu at least) is to add them to the 'admin' group.

Now, as this user:

    git clone https://github.com/pikesley/install-chef-server
    cd install-chef-server
    ./install-chef-server
