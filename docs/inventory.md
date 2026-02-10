# Ansible Inventory

The inventory is just a list of all managed nodes you want to target with playbooks or ad hoc commands.
Set it with the `-i` option or define it in `ansible.cfg`. This repo points to `hosts.ini` by default.

If the controller username exists on the target, no extra vars are needed; otherwise set `ansible_user` and `ansible_become_password` in the inventory.

```cfg
[defaults]
INVENTORY = path/to/inventory
```

Two common formats exist: [ini style](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/ini_inventory.html) and YAML. This repo uses ini style because it is simpler.

`hosts.ini`

```ini
[group]
host1
host2

[group2]
host1
host3
```

Aliases can be defined for individual hosts along with other [parameters](https://docs.ansible.com/ansible/latest/inventory_guide/intro_inventory.html#connecting-to-hosts-behavioral-inventory-parameters), for example

```ini
host_alias	ansible_host=IP	ansible_port=port	ansible_user=kali	ansible_connection=ssh
```

Now the alias can be used with ansible commands

```sh
ansible host_alias -i hosts.ini -m <module>
```

Similarly variables can be defined for individual hosts, groups, or all hosts

```ini
[group:vars]
variable=value

[all:vars]
key=value
```

Limit which group or host to target with the `--limit=<pattern>` option
