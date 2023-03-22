# role-debian_base

Ansible role to setup a 'freshly' installed Debian 11 server (non-GUI).

> Be aware that this role is highly opinionated and fits my preferences and way of working.
> It may or may not be suitable for your needs.

## Role variables

### Mandatory variables

`main_domain` must be defined tocreate an entry in the Debian `/etc/hosts` file.

`shell_user` must be defined for user-based configuration (like ZSH, Locale, etc.).

These variables can be specified in the `hosts.yaml` inventory file or in the `vars`section of an Ansible playbook, like this:

```yaml
- hosts: all
  become: true
  gather_facts: true
  vars:
    main_domain: example.com
    shell_user: myuser
  roles:
    - debian-base
  ...
```

### Important optional parameters (with defaults)

`install_in_vm: true` - Indicate of we install in a VM and need to add the QEMU agent.

`install_p10k: false` - Indicates if p10k should be installed on top of ZSH.

`install_lh_prereq: false` - Indicated is the Longhorn prerequisites must be installed.

`install_neofetch: false` - Indicates if we want to have a fancy login/MOTD screen.

`install_locale: "en_US.UTF-8"` - Specifies the locale the set for the shell user (blank will not set the locale).

`system_timezone: Europe/Amsterdam` - Specify the correct time zone.

## Usage of this role

To use this role, include the following section in a `requirements.yml` file in the local `roles` directory:

```yaml
# Include the 'debian-base` role from GitHub
- src: git@github.com:crazyelectron-io/role-debian_base.git
  scm: git
  version: master
  name: debian-base
```

> Only include the 'top' roles, dependencies - when listed in `meta/main.yml` of the imported role - will be downloaded automatically.

To retrieve roles like this in your project, run `ansible-galaxy install -r roles/requirements.yml`.
Because these roles will not be updated locally when the repository is changed, to refresh an already retrieved role use `ansible-galaxy install -f -r roles/requirements.yml`

## Suggested project structure

├── inventories
│   ├── dev
│   │   ├── group_vars/
│   │   └── hosts.ini
│   └── prod
│       ├── group_vars/
│       └── hosts.ini
├── group_vars/
├── host_vars/
├── files/
├── templates/
├── roles
│   ├── localy_versionned_role1/
│   ├── localy_versionned_role2/
│   ├── requirements.yml
│   ├── .gitignore
├── ansible.cfg
├── README.md
├── some_playbook.yml
├── other_playbook.yml

Create `roles/.gitignore`:

```gitignore
#Ignore everything in dir...
/*
# ... but current file...
!.gitignore
# ... external role requirement file
!requirements.yml
# ... and configured custom/local roles
!localy_versionned_role*/
```

Add `roles_path = roles` to `ansible.cfg` to make sure that roles are searched and downloaded in our local folder.

### Workflow to deploy from the project

1. Clone the project repository
2. Download the external roles: `ansible-galaxy install -r roles/requirements`
3. Launch your playbook: `ansible-playbook -i inventories/dev some_playbook.yml`
