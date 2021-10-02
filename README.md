Server Setup
=========

[![CI](https://github.com/scottharwell/ansible-role-server-setup/workflows/CI/badge.svg)](https://github.com/scottharwell/ansible-role-server-setup/actions?query=workflow%3A+CI)

This is a simple role to configure Linux servers with a base set of packages and shell configuration that I prefer.  It will configure a list of users on the remote Linux servers with Fish shell as the default shell, will display NeoFetch on login, will configure Fish with Oh My Fish and the _Bob the Fish_ theme, and will setup VIM with my preferred configuration.  Currently, it supports RHEL, Debian, and Arch Linux distributions and FreeBSD.

Requirements
------------

Ansible >= 2.9

Run `ansible-galaxy install -r requirements.yml` to ensure that Ansible has dependencies for this role installed.

Role Variables
--------------

* `groups_to_create`: A list of groups that should exist on the remote servers.
  ```yaml
  groups_to_create:
    - www
  ```
* `users`: A list of users on the remote server to create or update. This is typically the default user to login to the server and ultimately the `scott` account that I configure for myself.
  ```yaml
  users:
    - name: scott
      admin: true # add to wheel or sudo
      groups:
        - some_group
  ```
* `passwordless_sudo`: Enables passwordless privilege escalation to the `wheel` or `sudo` groups.
* `ssh_public_keys`: A list of SSH public keys that will be added to the `authorized_keys` file for all of the users added.  These are local files copied to the remote server.
  ```yaml
  ssh_public_keys:
    - "{{ lookup('file', lookup('env','HOME') + '/.ssh/id_ed25519.pub') }}"
    - "{{ lookup('file', lookup('env','HOME') + '/.ssh/id_rsa.pub') }}"
  ```
* `omf_install_script_url`: The location to the install script for Oh My Fish.
  ```yaml
  omf_install_script_url: "https://get.oh-my.fish"
  ```
* `ultimate_vim_git_url`: URL to the Git repo for Ultimate VIM.
  ```yaml
  omf_install_script_url: "https://github.com/amix/vimrc.git"
  ```

Dependencies
------------

* Ansible Collections
  * `community.general`

Example Playbook
----------------

Example playbook that configures my home VMs configured through Vagrant.

```yaml
---
- hosts: home
  become: true

  vars:
    ssh_public_keys:
      - "{{ lookup('file', lookup('env','HOME') + '/.ssh/id_ed25519.pub') }}"
      - "{{ lookup('file', lookup('env','HOME') + '/.ssh/id_rsa.pub') }}"
    groups_to_create:
      - some_group
    users:
      - name: vagrant
        admin: true
        groups:
          - some_group
      - name: scott
        admin: false

  roles:
    - role: ~/Programming/Ansible/roles/server-setup

```

License
-------

MIT
