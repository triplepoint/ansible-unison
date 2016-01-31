# Unison
Install and configure Unison file syncronization between the ansible host and the deploy target.

Applying this role to a host also creates some configuration files locally with which to configure Unison.  Specifically, the specified `unison_local_config_directory` is created (if necessary), and a Unison preferences file and an SSH config file for this host are generated in this directory.

While applying this role configures the local and remote hosts for a Unison sync, to initiate the sync you have to invoke Unison from the local host:
``` bash
unison <whatever you set `unison_target_name`>
```

This will only keep the two hosts synced as long as this command is left running.

# Requirements
None.

# Role Variables
- unison_target_name: project # The target name which Unison will use as a shorthand for this sync.
- unison_mount_directory: /vagrant/ # The directory to create and keep synched on the remote host.  Note that it will be modified when unison is executed!
- unison_local_config_directory: ~/.unison/ # Where on the local host is Unison expecting its configuration files to live? Note that it REQUIRES A TRAILING SLASH.
- unison_package_version: 2.48.3-1~eugenesan~trusty1 # The deb package version for Unison (this should be synchronzied pretty closely to the ansible host's version)

# Dependencies
None

# Example Playbook
    - hosts:
        - some-host
      sudo: yes
      roles:
        - unison

# License
MIT
