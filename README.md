Install chef-server from scratch
================================

Installing your own Chef server can be a bit hoop-jumpy. This abstracts all of those hoops into one (ugly) script which does all the jumping for you.

What does it do?
----------------

Chef server depends on a truckload of not-necessarily-easy-to-install things. We mostly follow [this](http://wiki.opscode.com/display/chef/Installing+Chef+Server+Manually) with a detour into RVM and Ruby 1.9.2.

The script:

+ Installs CouchDB
+ Installs and configures RabbitMQ (a more recent version than is in the Ubuntu repos)
+ Installs Sun Java 1.6 (using [this witchcraft](https://github.com/flexiondotorg/oab-java6)) - THIS IS NOW BROKEN, SEE BELOW
+ Installs OpsCode's Gecode
+ Installs RVM
+ Installs Ruby 1.9.2 (which isn't yet in the Ubuntu repos)
+ Installs the Chef gems
+ Configures the Chef server
+ Creates upstart scripts for each of the Chef components
+ Installs nginx to proxy the WebUI

Regarding Java, the delightful people at Oracle, always the friends of free software, have moved the goalposts and the oab-java6 thing no longer works. I know people have come up with workarounds for this, but no doubt these will get stepped on soon, too. Life is too short to spend it trying to outwit lawyer-driven corporations, so it now tests if Java is already installed, and if not, installs OpenJDK (which seems to work OK for me).

These steps are mostly idempotent.

How to use it
-------------

Dead-simple install:

    sudo apt-get install -y curl ; bash <(curl -s https://raw.github.com/gist/1869395/179c776758d8aae317b63fa87c447b821fb420a4/catering-college-installer)

What the above is actually doing: we create a new user (I've used 'chef') and add them to the 'admin' group (the shortest path to sudo happiness, at least on Ubuntu). Then, as this new user, checkout and run the script.

    NEWUSER=chef
    sudo useradd -m ${NEWUSER} -s /bin/bash
    cat /etc/group | sed "s/^\(admin:.*\)/\1,${NEWUSER}/" > /tmp/group && sudo mv /tmp/group /etc/
    sudo apt-get update
    sudo apt-get install -y git-core

    sudo su - ${NEWUSER} 

    git clone https://github.com/pikesley/catering-college
    cd catering-college
    ./install-chef-server

There's a trick here: `visudo`, then change this line:

    %admin ALL=(ALL) ALL

to

    %admin ALL=(ALL) NOPASSWD: ALL

to allow members of the 'admin' group to sudo without a password. You may not want to do this, of course.

Caveats
-------

+ I've tested this to death on an Ubuntu Lucid VM. I suspect it'll probably work on other Debian-ish platforms.
+ The configuration step for chef-solr assumes there's no data in there that we care about (which is obviously true for a new installation), and nukes the whole thing. There may be ways around this, but I don't know how Solr works and I really have no desire to.

