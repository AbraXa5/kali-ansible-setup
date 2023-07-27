# Customizing the playbook

## Customizing variables

Defaults for each role are defined in `defaults/main.yml`, which have a very low [precedence](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_variables.html#ansible-variable-precedence).

These defaults can be overridden setting values in the `vars/main.yml` file or command line options

## Customizing tasks

Each roles has a `tasks/main.yml` file which imports/ include other tasks. Unwanted tasks can be removed/ commented out from here

Post customization you can verify all available tasks using the command

```sh
ansible-playbook main.yml --list-tasks
```

## Customizing Roles

For this playbook, either edit the inline variables in the `main.yml` file in the root directory, or simply specify the required roles similar to `setup_kali.yml`
