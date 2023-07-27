# Ansible Inventory

The inventory is justa list of all managed nodes you want to automate the playbook/ ad hoc commands against
It can set using the `-i` option of define it in the `ansible.cfg` file

```cfg
[defaults]
INVENTORY = path/to/inventory
```

Two types of inventories, [ini style](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/ini_inventory.html) and yaml. I have used ini style inventories since its simpler

`hosts.ini`

```ini
[group]
host1
host2

[group2]
host1
host3
```

Aliases can be defined for individual hosts along with other other [parameters](https://docs.ansible.com/ansible/latest/inventory_guide/intro_inventory.html#connecting-to-hosts-behavioral-inventory-parameters), for example

```ini
host_alias	ansible_host=IP	ansible_port=port	ansible_user=kali	ansible_connection=ssh
```

Now the alias can be used with ansible commands

```sh
ansible host_alias -i hosts.ini -m <module>
```

Similarly variables for be defined for individual hosts, groups or all the hosts

```ini
[group:vars]
variable=value

[all:vars]
key=value
```

We can limit on which group/ host to run the ansible command/ playbook using the `--limit=<pattern>` option
