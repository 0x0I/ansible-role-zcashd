<p><img src="https://code.benco.io/icon-collection/logos/ansible.svg" alt="ansible logo" title="ansible" align="left" height="60" /></p>
<p><img src="https://previews.123rf.com/images/viktorijareut/viktorijareut1710/viktorijareut171000267/90109811-zcash-crypto-currency-block-chain-flat-logo.jpg" alt="zcash logo" title="zcash" align="right" height="80" /></p>

Ansible Role :lock_with_ink_pen: :link: Zcashd
=========
[![Galaxy Role](https://img.shields.io/ansible/role/46798.svg)](https://galaxy.ansible.com/0x0I/zcashd)
![GitHub release (latest by date)](https://img.shields.io/github/v/release/0x0I/ansible-role-zcashd?color=yellow)
[![Downloads](https://img.shields.io/ansible/role/d/46798.svg?color=lightgrey)](https://galaxy.ansible.com/0x0I/zcashd)
[![Build Status](https://travis-ci.org/0x0I/ansible-role-zcashd.svg?branch=master)](https://travis-ci.org/0x0I/ansible-role-zcashd)
[![License: MIT](https://img.shields.io/badge/License-MIT-blueviolet.svg)](https://opensource.org/licenses/MIT)

**Table of Contents**
  - [Supported Platforms](#supported-platforms)
  - [Requirements](#requirements)
  - [Role Variables](#role-variables)
      - [Install](#install)
      - [Config](#config)
      - [Launch](#launch)
      - [Uninstall](#uninstall)
  - [Dependencies](#dependencies)
  - [Example Playbook](#example-playbook)
  - [License](#license)
  - [Author Information](#author-information)

Ansible role that installs and configures Zcashd: a client for the Zcash zero-knowledge privacy blockchain/protocol.

##### Supported Platforms:
```
* Redhat(CentOS/Fedora)
* Ubuntu
* Debian
```

Requirements
------------
Requires the `unzip/gtar` utility to be installed on the target host. See ansible `unarchive` module [notes](https://docs.ansible.com/ansible/latest/modules/unarchive_module.html#notes) for details.

Role Variables
--------------
Variables are available and organized according to the following software & machine provisioning stages:
* _install_
* _config_
* _launch_
* _uninstall_

#### Install

`zcashd`can be installed using compressed archives (`.tar`, `.zip`) for debian-based installations, downloaded and extracted from various sources, or built from source.

_The following variables can be customized to control various aspects of this installation process, ranging from software version and source location of binaries to the installation directory where they are stored:_

`zcashd_user: <service-user-name>` (**default**: *zcashd*)
- dedicated service user and group used by `zcashd` for privilege separation (see [here](https://www.beyondtrust.com/blog/entry/how-separation-privilege-improves-security) for details)

`install_type: <archive>` (**default**: archive)
- **archive**: currently compatible with **tar** formats, installation of **Zcashd** via compressed archives results in the direct download of its component binaries, consisting of the `zcashd` blockchain service and cli software.

  **note:** archived installation binaries can be obtained from the official [releases](https://github.com/zcash/zcash/releases) site or those generated from development/custom sources.

`install_dir: </path/to/installation/dir>` (**default**: `/opt/zcashd`)
- path on target host where the `zcashd` binaries should be extracted to.

`archive_url: <path-or-url-to-archive>` (**default**: see `defaults/main.yml`)
- address of a compressed **tar or zip** archive containing `zcashd` binaries. This method technically supports installation of any available version of `zcashd`. Links to official versions can be found [here](https://github.com/zcash/zcash/releases).

`archive_options: <untar-or-unzip-options>` (**default**: `[]`)
- list of additional unarchival arguments to pass to either the `tar` or `unzip` binary at runtime for customizing how the archive is extracted to the designated installation directory. See [man tar](https://linux.die.net/man/1/tar) and [man unzip](https://linux.die.net/man/1/unzip) for available options to specify, respectively.

#### Config

**Zcashd** supports specification of various options controlling aspects of the Zcashd client's behavior and operational profile. Each configuration can be expressed within in a simple configuration file, `zcashd.conf` by default, composed of **key=vaue** pairs representing the aforementioned configuration properties available. The following details the facilities provided by this role to manage the contents of the aforementioned configuration file.

As indicated, available configuration options are organized according to the systems/subsystems they are used to manage. For a reference to the list of available configuration options, see [here](https://zcash.readthedocs.io/en/latest/rtd_pages/zcash_conf_guide.html).

`config_dir: </path/to/configuration/dir>` (**default**: `/home/{{ zcashd_user }}`)
- path on target host where the `zcashd` configuration file should be rendered

Each of these configurations can be expressed using the `zcashd_config` hash, which contains a list of various `zcashd` configuration options (hash) objects organized according to the following:
* **network-related** - settings related to client network broadcasting and listening properties
* **json-rpc** - JSON-RPC server interfacing properties
* **transaction-fee** - client properties managing cost of verifying network transactions
* **miscellaneous** - additional properties managing client mining and privacy protection operations

Each `zcashd_config` entry is a **key-value** pair representing the equivalent of a configuration setting as expected to be expressed by the `zcashd` server.

`[zcashd_config:]: <hash-expressed-in-YAML>` (**default**: *required*)
- configuration key-value pair to render within the configuration file

###### Example

 ```yaml
  zcashd_config:
    testnet: 1
    addnode: testnet.z.cash
    server: 1
    bind: 0.0.0.0
 ```

#### Launch

This role supports launching `zcashd` utilizing the [systemd](https://www.freedesktop.org/wiki/Software/systemd/) service management tool, which manages the service as a background process or daemon subject to the configuration and execution potential provided by its underlying management framework.

_The following variables can be customized to manage the service's **systemd** [Service] unit definition and execution profile/policy:_

`extra_run_args: <zcashd-cli-options>` (**default**: `[]`)
- list of `zcashd` commandline arguments to pass to the binary at runtime for customizing launch

Supporting full expression of `zcashd`'s [cli](https://zcash.readthedocs.io/en/latest/rtd_pages/user_guide.html#using-zcash) and, consequently the full set of configuration options as referenced and described above, this variable enables the launch to be customized according to the user's exact specification.

`custom_unit_properties: <hash-of-systemd-service-settings>` (**default**: `[]`)
- hash of settings used to customize the `[Service]` unit configuration and execution environment of the `zcashd` **systemd** service.

#### Uninstall

Support for uninstalling and removing artifacts necessary for provisioning allows for users/operators to return a target host to its configured state prior to application of this role. This can be useful for recycling nodes and perhaps providing more graceful/managed transitions between tooling upgrades.

_The following variable(s) can be customized to manage this uninstall process:_

`perform_uninstall: <true | false>` (**default**: `false`)
- whether to uninstall and remove all artifacts and remnants of this `zcashd` installation on a target host (**see**: `handlers/main.yml` for details)

Dependencies
------------

- 0x0i.systemd

Example Playbook
----------------
default example:
```
- hosts: all
  roles:
  - role: 0x0I.zcashd
```

License
-------

MIT

Author Information
------------------

This role was created in 2020 by O1.IO.
