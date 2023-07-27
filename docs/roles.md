# Ansible Roles

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
    -   **If you need to change any variable values, this is where you do it**
-   The files directory will contain files to be copied to the remote host
-   `tasks` is where the actual tasks are
    -   The `tasks/main.yml` is the main file which imports/ includes all other tasks in the roles
    -   Individual tasks cab be omitted from here

> _Only inline vars or command line assignment have greater precedence than the `var/main.yml` file_.

## Roles

Currently there are 5 roles

-   **customizations**
    -   Customize the xfce4 panel, config file are located at files/xfce4
    -   Disable power management
    -   Install fonts
    -   Install IDEs
    -   Customize firefox
-   **dotfiles**
    -   Install my [dotfiles](https://github.com/AbraXa5/dotfiles) and necessary packages
-   **first_run**
    -   Enable https repos and set berkeley mirror in the sources
    -   Change user shell to ZSH
    -   Set timezone
    -   Regenerate SSH keys
    -   Update repos and full upgrade
    -   Clean cache and unwanted packages and dependencies
    -   update database for mlocate
-   **install_tools**
    -   Install tools using apt
    -   Clone tool repos from github
    -   Download bianries and scripts to Tools dorectory
    -   Install python tools using pipx
    -   Install golang and go tools
    -   Install cargo
    -   Install nvm and latest stable node
    -   Install docker and pull necessary images
    -   Install fzf and shell integrations
    -   Install neo4j and bloodhound
    -   Setup wordlists
    -   Fix Evil WinRM ruby
    -   Download Caido
    -   Clean packages and cache
    -   Update database for mlocate
-   **notes**
    -   Clone my notes from private repos
