# role-debian_base

Ansible role for setup of a freshly installed Debian 11 server (non-GUI).

> This role is highly opinionated and fits my way of working.
> It may not be suitable for your needs.

## Mandatory variables

`main_domain` must be defined for ther hosts file

## Usage of this role

To use this role, include the following section in a `requirements.yml` file in your local `roles` directory:

```yaml
# Include my 'debian-base` role from GitHub
- src: git@github.com:crazyelectron-io/role-debian_base.git
  scm: git
  version: master
  name: debian-base
```

> Only include the 'top' roles, dependencies  -when listed in `meta/main.yml` of the role - will be downloaded automatically.

To retrieve the roles linke this in your project, run `ansible-galaxy install -r roles/requirements`.
Because these roles will not be updated locally when changed, to refresh an already retrieved role, run `ansible-galaxy -f -r roles/requirements.yml`

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

Add `roles_path` to `ansible.cfg` to make sure that roles are searched and downloaded in our local folder.

### Workflow to deploy from the project

1. Clone the project repository
2. Download the external roles: `ansible-galaxy install -r roles/requirements`
3. Launch your playbook: `ansible-playbook -i inventories/dev some_playbook.yml`
