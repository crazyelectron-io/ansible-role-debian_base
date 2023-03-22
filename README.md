# role-debian_base

Ansible role for setup of a freshly installed Debian 11 server (non-GUI)

## Mandatory variables

`main_domain` must be defined for ther hosts file

## project structure

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

## roles/.gitignore

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

## roles/requirements.yml

```gitignore
# Classic galaxy role
- src: galaxy_user.role_name

# Git available role
- src: git@git.service.com:path/to/repo.git
  scm: git
  version: master
  name: local_role_name
```

You only need to list "top" roles, dependencies (listed in meta/main.yml of the role) will be downloaded also.

## ansible.cfg

We make sure that roles are searched and downloaded in our local folder

```ini
roles_path = roles
```

## Workflow to deploy from the project

1. Clone the project repository
2. Download the external roles: `ansible-galaxy install -r roles/requirements`
3. Launch your playbook: `ansible-playbook -i inventories/dev some_playbook.yml`

## Going further

Downloading the roles as git workspaces

By default, ansible-galaxy downloads from git and removes the local repository structure (i.e. the .git directory). If you want to download the roles and keep working on them (change, commit, push...), you can keep the git structure with:

```shell
ansible-galaxy install -g -r roles/requirements
```

Note that this will write a meta/.galaxy_install_info file inside your role that git will see as new if you did not ignore already.

## Updating roles after first install

Roles will not be updated with a new version (especially from galaxy) if they are already installed. To force updating use:

```shell
ansible-galaxy -f -r roles/requirements.yml
```

of course you do this also to switch to git workspace version (or reset to version given in requirements file)

```shell
ansible-galaxy -f -g -r roles/requirements.yml
```
