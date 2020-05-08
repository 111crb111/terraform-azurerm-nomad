# Nomad Install Script

This folder contains a script for installing Nomad and its dependencies. You can use this script, along with the
[run-nomad script](https://github.com/hashicorp/terraform-azurerm-nomad/tree/master/modules/run-nomad) it installs to create a Nomad [Amazon Machine Image 
(Azure Image)](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/build-image-with-packer) that can be deployed in 
[Azure](https://azure.microsoft.com/) across an Scale Set using the [nomad-cluster module](https://github.com/hashicorp/terraform-azurerm-nomad/tree/master/modules/nomad-cluster).

This script has been tested on the following operating systems:

* Ubuntu 16.04
* Amazon Linux

There is a good chance it will work on other flavors of Debian, CentOS, and RHEL as well.



## Quick start

<!-- TODO: update the clone URL to the final URL when this Module is released -->

To install Nomad, use `git` to clone this repository at a specific tag (see the [releases page](../../../../releases) 
for all available tags) and run the `install-nomad` script:

```
git clone --branch <VERSION> https://github.com/hashicorp/terraform-azurerm-nomad.git
terraform-azurerm-nomad/modules/install-nomad/install-nomad --version 0.5.4
```

The `install-nomad` script will install Nomad, its dependencies, and the [run-nomad script](https://github.com/hashicorp/terraform-azurerm-nomad/tree/master/modules/run-nomad).
You can then run the `run-nomad` script when the server is booting to start Nomad and configure it to automatically 
join other nodes to form a cluster.

We recommend running the `install-nomad` script as part of a [Packer](https://www.packer.io/) template to create a
Nomad [Azure Managed Image](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/build-image-with-packer) (see the 
[nomad-consul-ami example](https://github.com/hashicorp/terraform-azurerm-nomad/tree/master/examples/nomad-consul-ami) for sample code). You can then deploy the Azure Image across a 
Scale Set using the [nomad-cluster module](https://github.com/hashicorp/terraform-azurerm-nomad/tree/master/modules/nomad-cluster) (see the 
[nomad-consul-colocated-cluster](https://github.com/hashicorp/terraform-azurerm-nomad/tree/master/examples/nomad-consul-colocated-cluster) and
[nomad-consul-separate-cluster](https://github.com/hashicorp/terraform-azurerm-nomad/tree/master/examples/nomad-consul-separate-cluster) examples for fully-working sample code).




## Command line Arguments

The `install-nomad` script accepts the following arguments:

* `version VERSION`: Install Nomad version VERSION. Required. 
* `path DIR`: Install Nomad into folder DIR. Optional.
* `user USER`: The install dirs will be owned by user USER. Optional.

Example:

```
install-nomad --version 0.5.4
```



## How it works

The `install-nomad` script does the following:

1. [Create a user and folders for Nomad](#create-a-user-and-folders-for-nomad)
1. [Install Nomad binaries and scripts](#install-nomad-binaries-and-scripts)
1. [Install supervisord](#install-supervisord)
1. [Follow-up tasks](#follow-up-tasks)


### Create a user and folders for Nomad

Create an OS user named `nomad`. Create the following folders, all owned by user `nomad`:

* `/opt/nomad`: base directory for Nomad data (configurable via the `--path` argument).
* `/opt/nomad/bin`: directory for Nomad binaries.
* `/opt/nomad/data`: directory where the Nomad agent can store state.
* `/opt/nomad/config`: directory where the Nomad agent looks up configuration.
* `/opt/nomad/log`: directory where the Nomad agent will store log files.


### Install Nomad binaries and scripts

Install the following:

* `nomad`: Download the Nomad zip file from the [downloads page](https://www.nomadproject.io/downloads.html) (the 
  version number is configurable via the `--version` argument), and extract the `nomad` binary into 
  `/opt/nomad/bin`. Add a symlink to the `nomad` binary in `/usr/local/bin`.
* `run-nomad`: Copy the [run-nomad script](https://github.com/hashicorp/terraform-azurerm-nomad/tree/master/modules/run-nomad) into `/opt/nomad/bin`. 


### Install supervisord

Install [supervisord](http://supervisord.org/). We use it as a cross-platform supervisor to ensure Nomad is started
whenever the system boots and restarted if the Nomad process crashes.


### Follow-up tasks

After the `install-nomad` script finishes running, you may wish to do the following:

1. If you have custom Nomad config (`.hcl`) files, you may want to copy them into the config directory (default:
   `/opt/nomad/config`).
1. If `/usr/local/bin` isn't already part of `PATH`, you should add it so you can run the `nomad` command without
   specifying the full path.
   


## Why use Git to install this code?

<!-- TODO: update the package managers URL to the final URL when this Module is released -->

We needed an easy way to install these scripts that satisfied a number of requirements, including working on a variety 
of operating systems and supported versioning. Our current solution is to use `git`, but this may change in the future.
See [Package Managers](https://github.com/hashicorp/terraform-azurerm-consul/blob/master/_docs/package-managers.md) for 
a full discussion of the requirements, trade-offs, and why we picked `git`.
