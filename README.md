# ansible-role-debian_base

Ansible role to setup a 'freshly' installed Debian 12 server (non-GUI).

> Be aware that this role is highly opinionated and fits my preferences and way of working.
> It may or may not be suitable for your needs.

> Due to limitations in the Debian autoinstall preseed mechanism, it is necessary to set the network configuration to simple switching mode on the two NIC's that are used for Ansible SSH access (`eno1` and `eno3`) before running this playbook.
> After this playbook has completed, the network configuration on the Debian hosts is changed to bonding (LACP) and the network switch must be set to that configuration as well before the next playbooks can be deployed.

## Role variables

### Mandatory variables

`search_domain` _must_ be defined to create an entry in the Debian `/etc/hosts` file and configure the network interface.

`shell_user` _must_ be defined for user-based configuration (like ZSH, Locale, etc.).

These variables can be specified in the `hosts.yaml` inventory file, in the `group_vars`, or in the `vars`section of an Ansible playbook, like this:

```yaml
- hosts: all
  become: true
  gather_facts: true
  vars:
    search_domain: example.local
    shell_user: myuser
  roles:
    - debian-base
  ...
```

In a `inventory/hosts.yaml` file:

```yaml
---
all:
  vars:
    search_domain: example.local
    shell_user: myuser
    ...
  children:
  ...
```

Or in a `inventory/group_vars/all.yaml` file (recommended):

```yaml
### --- Debian_base role parameters
# Shell user for Ansible
shell_user: myuser
# The primary domain
search_domain: example.local
```

### Important optional parameters (with defaults)

`install_in_vm: false` - Indicate if we install in a VM and need to add the QEMU agent.

`install_p10k: false` - Indicates if p10k should be installed on top of ZSH.

`install_lh_prereq: false` - Indicates if the Longhorn prerequisites must be installed.

`install_neofetch: false` - Indicates if we want to have a fancy login/MOTD screen.

`install_locale: en_US.UTF-8` - Specifies the locale to set for the shell user (blank will not set the locale).

`system_timezone: Europe/Amsterdam` - Specify the correct time zone.

`enable_ufw: true` - Enable UFW once we are confident we know all the required ports.

`load_netfilter: true` - Needed for Kubernetes network.

`primary_dns: 10.0.0.1` - Used for network inteface configuration.

`secondary_dns` - usef for network interface configuration (no default; ommitted if not specified).

`public_dns: 1.1.1.1 8.8.8.8` - Used for NIC configuration.

`ntp_pool: pool.ntp.org` -  Define the NTP pool to use.

## Usage of this role

To use this role, include the following section in a `requirements.yml` file in the local `roles` directory (see also `requirements.yam.example`):

```yaml
# Include the 'debian-base` role from GitHub
- src: git@github.com:crazyelectron-io/ansible-role-debian_base.git
  scm: git
  version: main
  name: debian-base
```

> Only include the 'top' roles, dependencies - when listed in `meta/main.yml` of the imported role - will be downloaded automatically.

To retrieve this role in a project run `ansible-galaxy install -r requirements.yaml`.
Because these roles will not be updated locally when the repository is changed, to refresh an already retrieved role use `ansible-galaxy install -f -r requirements.yaml`.

## Dependencies

This role depends on the `reboot`, `linux-sensors` and `debian-update` roles which are specified as dependency (see `./meta/main.yaml`) and included automatically.

## Workflow to deploy from the project

1. Clone the project repository
2. Adjust and rename the requirements example file.
3. Download the external roles: `ansible-galaxy install -r roles/requirements`
4. Launch your playbook: `ansible-playbook -i ./inventory some_playbook.yml [-u ANSIBLE_USER]`
