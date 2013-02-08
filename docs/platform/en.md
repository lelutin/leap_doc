LEAP Platform Tools

Platform recipes (leap_platform)
=================================

The LEAP platform recipes define an abstract service provider. It consists of puppet modules designed to work together to provide a system administrator everything they need to manage a service provider infrastructure that provides secure communication services.

Typically, a system administrator will not need to modify the LEAP platform recipes, although they are free to fork and merge as desired. Most service providers using the LEAP platform will use the same platform recipes.

The recipes are abstract. In order to configure settings for a particular service provider, a system administrator creates a provider instance. The platform recipes also include a base provider that provider instances inherit from.

Provider instance
================================

A "provider instance" is a directory tree (typically tracked in git) containing all the configurations for a service provider's infrastructure. A provider instance primarily consists of:

* A configuration file for each server (node) in the provider's infrastructure (e.g. nodes/vpn1.json)
* A global configuration file for the provider (e.g. provider.json).
* Additional files, such as certificates and keys (e.g. files/nodes/vpn1/vpn1_ssh.pub).
* A pointer to the platform recipes (as defined in "Leapfile")

A minimal provider instance directory looks like this:

    └── rewire                 # provider instance directory
        ├── common.json         # settings common to all nodes
        ├── Leapfile            # specifies which platform recipe directory to use
        ├── provider.json       # global settings of the provider
        ├── files/              # keys, certificates, and other files.
        ├── nodes/              # a directory for node configurations, one node per file
        └── users/              # public key information for privileged sysadmins

A provider instance directory contains everything needed to manage all the servers that compose a provider's infrastructure. Because of this, you can use normal git development work-flow to manage your provider instance.

Command line program (leap_cli)
=======================================

The command line program `leap` is used by sysadmins to manage everything about a service provider's infrastructure. Except when creating an new provider instance, `leap` is run from within the directory tree of a provider instance.

The `leap` command line has many capabilities, including:

* Create, initialize, and deploy nodes (e.g. servers)
* Manage keys and certificates
* Query information about the node configurations

Traditional system configuration automation systems, like puppet or chef, deploy changes to servers using a pull method. Each server pulls a manifest from a central master server and uses this to alter the state of the server.

Instead, LEAP uses a masterless push method: The user runs 'leap deploy' from the provider instance directory on their desktop machine to push the changes out to every server (or a subset of servers). LEAP still uses puppet, but there is no central master server that each node must pull from.

One other significant difference between LEAP and typical system automation is how interactions among servers are handled. Rather than store a central database of information about each server that can be queried when a recipe is applied, the `leap` command compiles static representation of all the information a particular server will need in order to apply the recipes. In compiling this static representation, `leap` can use arbitrary programming logic to query and manipulate information about other servers.

These two approaches, masterless push and pre-compiled static configuration, allow the sysadmin to manage a set of LEAP servers using traditional software development techniques of branching and merging, to more easily create local testing environments using virtual servers, and to deploy without the added complexity and failure potential of a master server.