# Unison
[![Build Status](https://travis-ci.org/triplepoint/ansible-unison.svg?branch=master)](https://travis-ci.org/triplepoint/ansible-unison)

## Introduction
Install and configure a [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) file synchronization between the Ansible host and the deploy target.

NOTE: As of right now, this only really works when the remote host is a Vagrant machine, due to the SSH configuration being extracted straight from Vagrant.  It wouldn't be _too_ much work to generalize this to optionally take in manually-written SSH config files, but for now this is it.

## Details
Applying this role to a host also creates some configuration files locally with which to configure Unison.  Specifically, the directory specified by the `unison_local_config_directory` variable is created (if necessary), and a Unison preferences file and an SSH config file for this host are generated in that directory.

## Installing Unison
In order for Unison to perform on the local host, Unison must be installed there.  While full instructions are outside the scope of this document, for Apple OSX users with [Homebrew](http://brew.sh/) installed, Unison is available there:
``` bash
brew install unison
```

### Unison Versions
It's said that if you don't have the same version of Unison on both ends of the sync, then _Bad Things_ can happen.  The Ansible variables `unison_package_repository` and `unison_package_version` define where to find Unison for the remote host, and care should be taken to make sure these match the version installed on the local host.  The installed version of Unison can be checked with:
``` bash
unison -version
```

## Starting and Stopping Unison
Applying this role configures the local and remote hosts for a Unison sync, but to initiate and maintain the sync you have to invoke Unison from the local host (substituting your value for `unison_project_name`):
``` bash
unison unison_project_name
```

This will only keep the two hosts synced as long as this command is left running.

Unison can be halted with `control-c`.

## Dealing with Unison Errors
In the following scenario:
- Running VM with Unison running
- Stop Unison
- Destroy the VM and recreate it
- Restart Unison as described above

Unison will error out, complaining about an inconsistent state, a missing archive on the remote host, and a local archive (it looks like a big hash string) that should be deleted.  Take it's advice and delete the local archive (substituting in the hash of the offending local archive in Unison's error message for `offending_local_archive`):
``` bash
rm -r ~/.unison/offending_local_archive
```

Once this is resolved, Unison can be started like normal.

# Requirements
`Unison` must be installed (see above).

# Role Variables
- `unison_project_name`: false _# Must be explicitly overridden.  The target name which Unison will use as a shorthand for this sync._
- `unison_local_mount_directory`: . _# The directory on the localhost with which to sync the remote directory defined by `unison_remote_mount_directory`._
- `unison_remote_mount_directory`: /vagrant _# The directory to create and keep synced on the remote host.  Note that it will be modified when Unison is executed!_
- `unison_local_config_directory`: ~/.unison _# Where on the local host is Unison expecting its configuration files to live?_
- `unison_package_repository`: ppa:eugenesan/ppa _# The package repository with which to install Unison._
- `unison_package_version`: 2.48.4-0~eugenesan~trusty1 _# The deb package version for Unison (this should be synchronzied pretty closely to the Ansible host's version)_

# Dependencies
None

# Example Playbook
    - hosts:
        - some-hosts-or-groups
      become: true
      roles:
        - {role: triplepoint.unison, unison_project_name: "some_project_name"}

# License
MIT
