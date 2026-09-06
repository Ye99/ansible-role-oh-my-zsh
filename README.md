Ansible Role: Oh My Zsh
=======================

[![Tests](https://github.com/gantsign/ansible-role-oh-my-zsh/workflows/Tests/badge.svg)](https://github.com/gantsign/ansible-role-oh-my-zsh/actions?query=workflow%3ATests)
[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-gantsign.oh--my--zsh-blue.svg)](https://galaxy.ansible.com/gantsign/oh-my-zsh)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](https://raw.githubusercontent.com/gantsign/ansible-role-oh-my-zsh/master/LICENSE)

Role to download, install and configure [Oh-My-Zsh](http://ohmyz.sh/).

Requirements
------------

* Ansible >= 5 (Ansible Core >= 2.12)

* Linux Distribution

    * Debian Family

        * Debian

            * Stretch (9)
            * Buster (10)
            * Bullseye (11)

        * Ubuntu

            * Bionic (18.04)
            * Focal (20.04)
            * Jammy (22.04)

    * RedHat Family

        * Rocky Linux

            * 8

        * Fedora

            * 35

    * SUSE Family

        * openSUSE

            * 15.3

    * Note: other versions are likely to work but have not been tested.

Usage:
--------------
git clone this repo anywhere; the clone directory can be named whatever you
like. This repo has my theme, plugins configured. To install ohmyzsh to a
computer, run `playbook.yaml` from the project root:
```
ansible-playbook -i 10.0.0.185, playbook.yaml
```

The playbook installs for the user named by the `zsh_username` variable, which
defaults to `ye` (see `defaults/main.yml`). To install for a different user,
override it on the command line:
```
ansible-playbook -i 10.0.0.185, -e "zsh_username=someuser" playbook.yaml
```

Role Variables
--------------

The following variables will change the behavior of this role (default values
are shown below):

```yaml
# Default theme
oh_my_zsh_theme: robbyrussell

# Default plugins
oh_my_zsh_plugins:
  - git

# Whether to install by default for all specified users.
# May be overridden by `oh_my_zsh: install:` under each user.
oh_my_zsh_install: true

# Default update mode for Oh-My-Zsh
# accepted values are:
# disabled (default)
# auto
# reminder
oh_my_zsh_update_mode: disabled

# Default update frequency in days. When the update mode is set to a value other
# than "disabled", this is the frequency (in days) to check for a new version.
# The value 0 will check every time a new shell session starts.
oh_my_zsh_update_frequency: 13

# Whether to write the ~/.zshrc file
# May be overridden by `oh_my_zsh: write_zshrc:` under each user.
oh_my_zsh_write_zshrc: true

# Whether to migrate PATH entries from the user's bash environment.
oh_my_zsh_migrate_bash_path: true

# User configuration
# Important: oh-my-zsh is installed per user so you need to specify the users to install it for.
users:
  - username: example1
    oh_my_zsh:
      theme: robbyrussell
      plugins:
        - git
      update_mode: reminder
      update_frequency: 3
      write_zshrc: false
  - username: example2
    oh_my_zsh:
      theme: robbyrussell
      plugins:
        - git
        - mvn
      update_mode: auto
      update_frequency: 10
  - username: example3
    oh_my_zsh:
      install: false
```

Migrating your bash PATH
------------------------

Switching the login shell to zsh silently breaks command line tools, because
zsh reads none of `~/.profile`, `~/.bash_profile` or `~/.bashrc`. Any `PATH`
entry those files contribute — and tool installers routinely append one — is
simply gone once you log in to zsh, so the tool stops resolving even though it
is still installed.

With `oh_my_zsh_migrate_bash_path: true` (the default) the role works out those
entries on the target host rather than guessing at them. It compares the `PATH`
of a bash started with `--noprofile --norc` against the `PATH` of a login +
interactive bash — the only mode that reads `~/.bashrc`, whose Debian default
returns early otherwise. Whatever the second has that the first does not is
exactly what the user's bash dotfiles added, and the generated `~/.zshrc`
re-adds each of those directories (if it still exists) in the same order.

Nothing is hardcoded, so this picks up machine-specific directories without the
role knowing about them. Set the variable to `false` to skip it.

Note the probe reflects the bash dotfiles **as they were when Ansible last
ran**; install something that appends to `~/.bashrc` afterwards and you need to
re-run the playbook to pick it up.

Local customizations
--------------------

The role rewrites `~/.zshrc` on every run, so edits to it are lost. Put
machine-local settings in `~/.zshrc.local` instead — the generated `~/.zshrc`
sources that file at the end if it exists, and the role never touches it.

Example Playbook
----------------

```yaml
- hosts: servers
  vars:
    zsh_username: johndoe
  roles:
    - role: gantsign.oh-my-zsh
      users:
        - username: "{{ zsh_username }}"
```

More Roles From GantSign
------------------------

You can find more roles from GantSign on
[Ansible Galaxy](https://galaxy.ansible.com/gantsign).

Development & Testing
---------------------

This project uses [Molecule](http://molecule.readthedocs.io/) to aid in the
development and testing; the role is unit tested using
[Testinfra](http://testinfra.readthedocs.io/) and
[pytest](http://docs.pytest.org/).

To develop or test you'll need to have installed the following:

* Linux (e.g. [Ubuntu](http://www.ubuntu.com/))
* [Docker](https://www.docker.com/)
* [Python](https://www.python.org/) (including python-pip)
* [Ansible](https://www.ansible.com/)
* [Molecule](http://molecule.readthedocs.io/)

Because the above can be tricky to install, this project includes
[Molecule Wrapper](https://github.com/gantsign/molecule-wrapper). Molecule
Wrapper is a shell script that installs Molecule and it's dependencies (apart
from Linux) and then executes Molecule with the command you pass it.

To test this role using Molecule Wrapper run the following command from the
project root:

```bash
./moleculew test
```

Note: some of the dependencies need `sudo` permission to install.

License
-------

MIT

Author Information
------------------

John Freeman

GantSign Ltd.
Company No. 06109112 (registered in England)
