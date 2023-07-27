# Ansible Kali Setup

[![asciicast](https://asciinema.org/a/598965.svg)](https://asciinema.org/a/598965?data-speed="3")

## Getting Started

Ansible is required on the controller host to run the playbook

```sh
pipx install ansible --include-deps
```

Or simply

```sh
sudo apt install ansible
```

Install community collections

```sh
ansible-galaxy collection install --upgrade -r requirements.yml
ansible-galaxy role install --force -r requirements.yml
```

## Usage

### Inventory

By default the playbook is set to run against all hosts defined in the `hosts.ini` file as per the `ansible.cfg` file
By default ansible connects to the host with the username the playbook is run as, if this user doesn't exist on the host then it can be defined in the inventory files as `ansible_user`

To add/ edit hosts, edit the inventory file, for example

```ini
[group]
host ansible_user='user'  ansible_become_password='password'

[group:vars]
variable=value
```

An entry like this depends on ssh config for the host ip and identity key

```ini
kali.vm    ansible_user=kali  ansible_become_password='kali'
```

### SSH Connection

To set up key based authentication

```bash
ssh-keygen -f ~/.ssh/id_rsa -C "kali@kali.vm"
ssh-add ~/.ssh/id_rsa
ssh-copy-id -i ~/.ssh/id_rsa kali@192.168.253.138
```

The SSH config at `.ssh/config` should look like

```bash
Host kali.vm
  HostName 192.168.253.138
  User kali
  IdentityFile ~/.ssh/id_rsa
  IdentitiesOnly yes
```

Now ansible knows how to connect to the host via SSH using the identity key

If you don't want to setup authN keys, password based authN can be done by setting these variables and installing the `sshpass` package

-   ansible_host
-   ansible_user
-   ansible_become_password
-   ansible_connection
-   ansible_ssh_user
-   ansible_ssh_pass

Now the playbook can be run as,

```sh
ansible-playbook main.yml --limit=kali.vm
```

> If you don't to save passwords in a file, the `--ask-become-pass` flag can be used to pass the password at runtime. Ansible vault is also a good option

### Local Connection

If you rather run the playbook locally on the VM that can be done by

```sh
ansible-playbook main.yml --limit=local -K
```

> The local group is already defined in the inventory file

### Roles

The playbook has multiple roles with multiple tasks

-   first_run
    -   Change Kali repo and add https support
-   dotfiles
    -   Install my [dotfiles](https://github.com/AbraXa5/dotfiles)
-   install_tools
    -   Install/ download required tools and binaries
-   customizations
    -   Customize xfce4 panel, power settings and browser
-   notes
    -   Clone my notes from private repos

This playbook setups my VM as per my preferences, and also uses private keys in certain roles (for example, notes), so comment out/ remove the roles you don't want from `main.yml` or `setup_kali.yml`.

### Customizing Roles

Role are located at `roles/<role_name>`. Each role has a directory structure similar to

```
├── defaults
│   └── main.yml
├── files
├── README.md
├── tasks
│   |── task1.yml
│   |── task2.yml
│   |── task3.yml
│   └── main.yml
└── vars
    └── main.yml
```

-   `defaults` will contain default values for all variables
-   `vars` also contains values for variables used in the playbook but these have more precedence than default.
    -   **If you need to change any variable values, this is where you do it**. _Only inline vars or command line assignment have greater precedence than the `var/main.yml` file_.
-   The files directory will contain files to be copied to the remote host
-   `tasks` is where the actual tasks are
    -   The `tasks/main.yml` is the main file which imports/ includes all other tasks in the roles
    -   Individual tasks cab be omitted from here

Post customization you can list all available tasks using the command

```sh
ansible-playbook main.yml --list-tasks
```

## Executing the playbook

After modifying the inventory and roles, the playbook can be executed as

```sh
ansible-playbook main.yml --limit=vm
```

Or

```sh
ansible-playbook main.yml --limit=local -K
```
