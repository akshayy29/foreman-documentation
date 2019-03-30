1. Plugins
==========

Plugins are tools to extend and modify the functionality of Foreman, Smart Proxy and Hammer CLI.
The core Foreman applications are designed to be lean, to maximize flexibility and to minimize code
bloat. Plugins offer custom functions and features so that each user can tailor their environment
to their specific needs.

A plugin project may consist of a single Foreman or Smart Proxy plugin, or often a Foreman plugin,
Smart Proxy plugin, and a Hammer CLI plugin working together.

Foreman plugins are implemented as Rails engines, and Foreman, Smart Proxy and Hammer CLI plugins
are all packaged as gems, RPMs and Debian packages.

1.1 Popular Plugins
^^^^^^^^^^^^^^^^^^^




1.2 More plugins
^^^^^^^^^^^^^^^^^
Full lists of plugins are available:

Foreman plugins
Smart proxy plugins
Hammer CLI plugins

1.3 Installation
^^^^^^^^^^^^^^^^^

Foreman, Smart Proxy and Hammer CLI plugins are packaged as gems, but the Foreman project provides RPMs and Debian packages to ease installation with standard Foreman packages. See below for the different installation methods, which depend on your platform.

The number of plugins available in the project repositories is increasing, so check the wiki to see if a package is available yet. If it’s a useful or popular plugin and not yet packaged, please file a feature request in the packaging project (see Bug Reporting for more details on reporting issues).

1.3.1 Using the Foreman installer
----------------------------------

Some plugins can be installed using the Foreman installer, which can help with additional configuration that the plugin may require, e.g. config files or services.

To see which plugins the installer can set up, run:

# foreman-installer --help | grep enable-
    --[no-]enable-foreman-plugin-discovery Enable 'foreman_plugin_discovery' puppet module (default: false)
    --[no-]enable-foreman-plugin-docker Enable 'foreman_plugin_docker' puppet module (default: false)
    --[no-]enable-foreman-plugin-hooks Enable 'foreman_plugin_hooks' puppet module (default: false)

Run the installer with the extra --enable... argument to add the plugin. Any arguments given when
Foreman was first installed will be kept the same, saved in the installer answers file.


Note that the installer will enforce the state of all managed configuration files, so manual changes will be reverted. Use --noop -v first to check for any unexpected changes.

foreman-installer --noop -v --enable-foreman-plugin-discovery
Run again without noop to make the changes:

foreman-installer --enable-foreman-plugin-discovery
Some plugins have additional parameters, which can be changed using foreman-installer -i for interactive mode or found in the foreman-installer --help output.

1.3.2 Package installation
---------------------------

Select operating system
To provide specific installation instructions, please select your operating system:

The repositories are available at https://deb.theforeman.org plugins <version>. Separate repositories are available for each Foreman release, containing plugins that are compatible with that particular version. They are signed with the Foreman APT key.

If not already configured, add the repo by editing /etc/apt/sources.list.d/deb_theforeman_org.list and adding the following line:

deb http://deb.theforeman.org/ plugins 1.21
To install a plugin:

Find the package for the plugin: apt-cache search discovery
Install the package, e.g. apt-get install ruby-foreman-discovery
Restart Foreman: touch ~foreman/tmp/restart.txt or service apache2 restart
Some plugins (e.g. foreman_column_view) may also require configuration in /etc/foreman/plugins/, check for any .example files. Smart proxy plugins can be configured in /etc/foreman-proxy/settings.d/.

The naming of packages is as follows:

Packages for Foreman have a ruby-foreman- prefix.
Smart proxy packages have a ruby-smart-proxy- prefix.
Hammer CLI packages have a ruby-hammer-cli- prefix.

1.3.4 Advanced Installation from Gems
-------------------------------------

Not recommended, as it’s possible for the ‘gem’ command to install other, newer dependencies, which can cause problems with the Foreman installation. Do note the install without dependencies below to avoid this problem.

Ensure the plugin you want is available from rubygems.org as a gem. Plugins that aren’t published (just git repos) can’t be installed with this method without being built as a gem.

1.3.5 Red Hat distributions
-------------------------------------

If on EL7, run scl enable tfm bash first for an SCL-enabled shell.

Install without dependencies: gem install --ignore-dependencies foreman_column_view
If you need other dependencies (see the rubygems.org page), check the yum repo above (e.g. deface, nokogiri) or install the same way with ‘gem’
Add to the bundler.d/Gemfile.local.rb file as detailed below.
Restart Foreman with service foreman restart
If you hit problems, uninstall the added gems with <pre>gem uninstall -v VERSION GEM</pre>

1.3.6 Debian distributions
---------------------------

It is recommended to use ~foreman/bundler.d/Gemfile.local.rb so that it is not overwritten by future upgrades.

If it’s published on rubygems.org, just add the name and the latest released version will be downloaded. Add to bundler.d/Gemfile.local.rb:
gem 'foreman_sample_plugin'
Or bundler can install the plugin from a git repository. Add to bundler.d/Gemfile.local.rb:
gem 'foreman_sample_plugin', :git => "https://github.com/example/foreman_sample_plugin.git"
Next, as user foreman (not root!), run the following command: $ /usr/bin/foreman-ruby /usr/bin/bundle install
Once the plugin has been installed for the first time, you can use $ /usr/bin/foreman-ruby /usr/bin/bundle update foreman_sample_plugin to update it
Then restart Foreman with touch ~foreman/tmp/restart.txt

1.4 Writing Your Own
^^^^^^^^^^^^^^^^^^^^

More information about writing your own plugins is available in the wiki:

How to Create a Foreman Plugin
How to Create a Smart Proxy Plugin
Hammer CLI developer documentation
The Foreman development forum and IRC channels can be of help for plugin developers.

1.4.1 Making your plugin official
---------------------------------

We are happy to help bring your plugin to the Foreman Community! We offer:

Automated testing of your plugin in our Jenkins CI system
Adding it to our packaging repos so users can yum/apt install it
Hosting the plugin code under our ‘theforeman’ organization on GitHub
An issue tracker on our Redmine instance (or use GitHub issues)
We do ask for a few things in return:

Releases are tagged, ideally using SemVer
Repos (on GitHub) and gems (on Rubygems) have an extra maintainer added
This is so that we can help with maintenance if you become too busy or move on from the plugin
You’re welcome to request a specific member of the development community to be the additional author, otherwise we’ll recommend someone
