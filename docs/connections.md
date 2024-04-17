# Connecting to mananged nodes

The ideal way of connecting to unix nodes is via SSH

The SSH conn can be key based or password based.

## Key based authN

Start and enable SSH at startup on the remote box

```sh
sudo systemctl enable ssh --now
```

Generate SSH key, add it to the authN agent and copy the key to the remote box

```sh
ssh-keygen -t rsa -f ~/.ssh/id_rsa -C "username@host_alias.group"
ssh-add ~/.ssh/id_rsa
ssh-copy-id -i ~/.ssh/id_rsa user@host
```

Additionally adding an entry to the ssh config at `~/.ssh/config` can reduce the number of parameters to add to the inventory file

```sh
Host host_alias
  HostName IP
  User kali
  IdentityFile ~/.ssh/id_rsa
  IdentitiesOnly yes
```

Now, an inventory like this is enough to create a connection

```ini
host_alias ansible_user='user'
```

## Password based authN

Without ssh keys, python based ssh programs like sshpass or paramiko are required

The inventory file supports [additional parameters](https://docs.ansible.com/ansible/latest/inventory_guide/intro_inventory.html#connecting-to-hosts-behavioral-inventory-parameters) like

- ansible_host
- ansible_user
- ansible_become_password
- ansible_connection
- ansible_ssh_user
- ansible_ssh_pass

```ini
host_alias ansible_host=IP ansible_user=kali ansible_become_password='user_passwd' ansible_connection=ssh ansible_ssh_user=kali ansible_ssh_pass='ssh_passwd'
```

## Local Connection

Adding an entry to the inventory and setting the connection to local

```ini
localhost ansible_connection=local
```

The playbook can be run only on localhost by running

```sh
ansible-playbook main.yml --limit=localhost
```

Alternatively, the main.yml file can be edited to set host tolocalhost from all

```yml
- name: Configure kali
  hosts: localhost
```
