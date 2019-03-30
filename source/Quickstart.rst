0. Quickstart
=============

The Foreman installer is a collection of Puppet modules
that installs everything required for a full working Foreman
setup. It uses native OS packaging (e.g. RPM and .deb packages)
and adds necessary configuration for the complete installation.

Components include the Foreman web UI, Smart Proxy, Passenger,
a Puppet master (either Puppet Server or under Passenger), and
optionally TFTP, DNS and DHCP servers. It is configurable and
the Puppet modules can be read or run in “no-op” mode to see
what changes it will make.

Supported platforms

* CentOS, Scientific Linux or Oracle Linux 7, x86_64
* Debian 9 (Stretch), i386/amd64/aarch64
* Red Hat Enterprise Linux 7, x86_64
* Ubuntu 16.04 (Xenial), i386/amd64/aarch64
* Ubuntu 18.04 (Bionic), i386/amd64/aarch64
* Other operating systems will need to use alternative installation methods (see the manual).

The installation will require 4GB of memory, see `System Requirements <https://www.theforeman.org/manuals/1.21/index.html#3.1SystemRequirements>`_ for more information.

0.1 Installation
----------------

`The Foreman installer <https://www.theforeman.org/manuals/1.21/index.html#3.2ForemanInstaller>`_ uses Puppet (4 or later required) to
install Foreman. This guide assumes that you have a newly
installed operating system, on which the installer will
setup Foreman, a Puppet master, and the `Smart Proxy <https://www.theforeman.org/manuals/1.21/index.html#4.3SmartProxies>`_ by default.
It’s not advisable to follow the steps below on an existing
system, since the installer will affect the configuration of several components.

Select operating system
To provide specific installation instructions, please select your operating system:

Repositories
No operating system selected.
Downloading the installer
No operating system selected.
Running the installer
The installation run is non-interactive, but the configuration can be customized by supplying any of the options listed in foreman-installer --help, or by running foreman-installer -i for interactive mode. More examples are given in the Installation Options section. Adding -v will disable the progress bar and display all changes. To run the installer, execute:

>>> sudo foreman-installer

After it completes, the installer will print some details about where to find Foreman and the Smart Proxy and Puppet master if they were installed along Foreman. Output should be similar to this:

* Foreman is running at https://theforeman.example.com
    Initial credentials are admin / 3ekw5xtyXCoXxS29
* Foreman Proxy is running at https://theforeman.example.com:8443
* Puppetmaster is running at port 8140

The full log is at /var/log/foreman-installer/foreman-installer.log

0.2 Puppet Management
---------------------

After installation, the Foreman installer will have set up a puppet master on the host, fully integrated with Foreman. First run the Puppet agent on the Foreman host which will send the first Puppet report to Foreman, automatically creating the host in Foreman’s database.

>>> sudo puppet agent --test

Puppet will show a warning the first time that the node can't be found, this can be ignored.
In Foreman, click on the Hosts tab and your Foreman host should be visible in the list with an “O” status. This indicates its status is OK, with no changes made on the last Puppet run.

Downloading a Puppet module
Next, we’ll install a Puppet module for managing the NTP service from Puppet Forge to our “production” environment (the default):

>>> sudo puppet module install puppetlabs/ntp

In Foreman, go to Configure > Classes and click Import from hostname (top right) to read the available Puppet classes from the puppet master and populate Foreman’s database. The “ntp” class will appear in the Puppet class list if installed correctly.

Using the Puppet module
Click on the “ntp” class in the list, change to the Smart Class Parameters tab and select the servers parameter on the left hand side. Tick the Override checkbox so Foreman manages the “servers” parameter of the class and change the default value if desired, before submitting the page.

More info: `Parameterized classes documentation <https://www.theforeman.org/manuals/1.21/index.html#4.2.5ParameterizedClasses>`_
Screencast: `Parameterized class support in Foreman <http://www.youtube.com/watch?v=Ksr0tilbmcc>`_
Change back to the Hosts tab and click Edit on the Foreman host. On the Puppet Classes tab, expand the ntp module and click the + icon to add the ntp class to the host, then save the host.

Managed parameters can be overridden when editing an individual host from its Parameters tab.
Clicking the YAML button when back on the host page will show the ntp class and the servers parameter, as passed to Puppet via the ENC (external node classifier) interface. Re-run puppet agent --test on the Foreman host to see the NTP service automatically reconfigured by Puppet and the NTP module.

Adding more Puppet-managed hosts
Other hosts with Puppet agents installed can use this puppet master by setting server = foreman.example.com in puppet.conf. Sign their certificates in Foreman by going to Infrastructure > Smart Proxies > Certificates or using puppet cert list and puppet cert sign on the puppet master.

Puppet classes can be added to host groups in Foreman instead of individual hosts, enabling a standard configuration of many hosts simultaneously. Host groups are typically used to represent server roles.
