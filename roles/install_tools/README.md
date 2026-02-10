# install_tools

Installs common tooling: apt packages, binary downloads, Go tools, pipx apps, Docker images, and supporting repos.

## Variables
- Source of truth: vars/main.yml (versions, repo lists, packages). Prefer editing here.
- defaults/main.yml is kept for legacy compatibility; it may lag behind vars.
- Optional: set pull_docker_images to true to pre-pull docker_images.

## Usage
- Included from the top-level playbooks; override variables via vars/main.yml or extra vars.
