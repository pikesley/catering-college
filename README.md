Install chef-server from scratch
================================

Installing your own Chef server can be a bit hoop-jumpy. This abstracts all of those hoops into one (ugly) script which does all the jumping for you.

What does it do?
----------------

Chef server depends on a truckload of not-necessarily-easy-to-install things. We mostly follow [this](http://wiki.opscode.com/display/chef/Installing+Chef+Server+Manually) with a detour into RVM and Ruby 1.9.2.

The script:

+ Installs CouchDB
+ Installs and configures RabbitMQ
+ Installs Sun Java 1.6 (using [this witchcraft](https://github.com/flexiondotorg/oab-java6))
+ Installs OpsCode's Gecode
+ Installs RVM
+ Installs Ruby 1.9.2 (which isn't yet in the Ubuntu repos)
+ Installs the Chef gems (with rvmsudo, which is awesome)
+ Configures the Chef server
+ Creates upstart scripts for each of the Chef components
+ Installs nginx to proxy the Chef server WebUI

These steps are mostly idempotent.

How to use it
-------------

We'll create a new user (I've used 'chef') and add them to the 'admin' group (the shortest path to sudo happiness, at least on Ubuntu). Then:

    NEWUSER=chef
    sudo useradd -m ${NEWUSER} -s /bin/bash
    cat /etc/group | sed "s/^\(admin:.*\)/\1,${NEWUSER}/" > /tmp/group && sudo mv /tmp/group /etc/
    sudo apt-get update
    sudo apt-get install -y git-core

    sudo su - ${NEWUSER} 

    git clone https://github.com/pikesley/catering-college
    cd install-chef-server
    ./install-chef-server

There's a trick here: `visudo`, then change this line:

    %admin ALL=(ALL) ALL

to

    %admin ALL=(ALL) NOPASSWD: ALL

to allow members of the 'admin' group to sudo without a password. You may not want to do this, of course.

Caveats
-------

+ I've tested this to death on an Ubuntu Lucid VM. I suspect it'll probably work on other Debian-ish platforms.
+ The WebUI will be set as the default nginx vhost - we're basically assuming that it'll be the only vhost on this box (which seems to me to be a safe assumption in a world where everything is virtualised).
+ The configuration step for chef-solr assumes there's no data in there that we care about (which is obviously true for a new installation), and nukes the whole thing.

