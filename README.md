# Ansible Kali Setup

> An opinionated ansible playbook for setting up a Kali VM idempotently for CTFs.

[![ascii-cast](https://asciinema.org/a/598965.svg)](https://asciinema.org/a/598965?data-speed="3")

> _Note: the recording above may not reflect the current state of the playbook._

## Getting Started

Ansible is required on the controller host to run the playbook

Tested with Ansible 2.17+ on Debian/Ubuntu/Kali controllers.

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

Quick sanity checks

```sh
ansible-playbook main.yml --syntax-check
ansible-playbook main.yml --list-tasks
```

## Usage

### Inventory

By default the playbook runs against hosts in `hosts.ini` (set via `ansible.cfg`). Ansible connects with the controller username unless you set `ansible_user` in the inventory.

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

### Connection Types

#### SSH Connection

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

- ansible_host
- ansible_user
- ansible_become_password
- ansible_connection
- ansible_ssh_user
- ansible_ssh_pass

Now the playbook can be run as,

```sh
ansible-playbook main.yml --limit=kali.vm
```

> If you don't want to save passwords in a file, the `--ask-become-pass` flag can be used at runtime. Ansible vault is also a good option

> **Security note**: The shipped `hosts.ini` contains default Kali credentials (`kali`/`kali`). These should be changed before use. For shared or versioned inventories, use [Ansible Vault](https://docs.ansible.com/ansible/latest/vault_guide/index.html) to encrypt sensitive values instead of storing them in plaintext.

#### Local Connection

If you rather run the playbook locally on the VM that can be done by

```sh
ansible-playbook main.yml --limit=local -K
```

> The local group is already defined in the inventory file

*Make sure to reboot the VM post running the playbook. Since this is a local connection, ansible won't reboot it. Doing so would shut the control node which in this case is the VM itself.*

#### Connecting to a VM on windows host

Port forward to access the VM's SSH port on localhost port 2222

```bash
ssh -p 22 -L 2222:{VM IP on VMware adapter}:22 username@{HOST IP}
```

Add this entry to hosts.ini

```bash
kali.fwd   ansible_host=localhost   ansible_port=2222  ansible_user=kali  ansible_become_password='kali' ansible_connection=ssh ansible_ssh_user=kali ansible_ssh_pass=kali
```

### Roles

The playbook has multiple roles with multiple tasks

- first_run
  - Enable https repos and set berkeley mirror in the sources
  - Change user shell to ZSH
  - Set timezone
  - Regenerate SSH keys
  - Update repos and full upgrade
  - Clean cache and unwanted packages
  - Update database for mlocate
- dotfiles
  - Install my [dotfiles](https://github.com/AbraXa5/dotfiles)
- install_tools
  - Install/ download required tools and binaries
  - Versions are maintained in `roles/install_tools/vars/main.yml`
- customizations
  - Customize xfce4 panel, power settings and browser
- notes
  - Clone my notes from private repos

This playbook setups my VM as per my preferences, and also uses private keys in certain roles (for example, notes), so comment out/ remove the roles you don't want from `main.yml` or `setup_kali.yml`.

> **`main.yml` vs `setup_kali.yml`**: `main.yml` is the recommended entrypoint — it uses conditional variables (`new_vm`, `setup_dotfiles`, `clone_notes`, `customize`) so you can selectively enable/disable roles at runtime. `setup_kali.yml` runs all 5 roles unconditionally and is intended for a full fresh setup.

For roles needing private repos (dotfiles, notes), ensure the controller has SSH access to those repos; use vault or runtime prompts instead of storing passwords in inventory if needed.

### Customizing Roles

Role are located at `roles/<role_name>`. Each role has a directory structure similar to

```sh
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

- `defaults` will contain default values for all variables
- `vars` also contains values for variables used in the playbook but these have more precedence than default.
  - **If you need to change any variable values, this is where you do it**. *Only inline vars or command line assignment have greater precedence than the `var/main.yml` file*.
- The files directory will contain files to be copied to the remote host
- `tasks` is where the actual tasks are
  - The `tasks/main.yml` is the main file which imports/ includes all other tasks in the roles
  - Individual tasks cab be omitted from here

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

> A couple tasks depend on the facts gathered during the playbook, if you indent to turn that off, modify the tasks accordingly. Search for `ansible.builtin.set_fact` in the codebase.
