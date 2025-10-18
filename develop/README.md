# Docker Raspberry Pi Test Environment

Test environment with Debian containers simulating Raspberry Pi systems for Ansible role development.

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/) & [Docker Compose](https://docs.docker.com/compose/install/)
- [Go Task](https://taskfile.dev/) - `brew install go-task/tap/go-task`
- [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/) - `pip install ansible`
- [sshpass](https://github.com/kevinburke/sshpass) - `brew install hudochenkov/sshpass/sshpass`

## Containers

| Name | Hostname | Internal IP | SSH Port | User/Pass |
|------|----------|-------------|----------|-----------|
| app1 | pis-app1.test | 192.168.60.4 | 2221 | pidev/pidev |
| app2 | pis-app2.test | 192.168.60.5 | 2222 | pidev/pidev |
| db | pis-db.test | 192.168.60.6 | 2223 | pidev/pidev |

User `pidev` has sudo access (password: `pidev`).

## SSH Access

```bash
# Direct SSH
ssh pidev@127.0.0.1 -p 2221

# With sshpass
sshpass -p 'pidev' ssh -p 2221 pidev@127.0.0.1

```

## Task Commands & Ansible Usage

```bash
# Test connectivity
task ping

# Test all containers
task test

# Rebuild containers
task rebuild

# Run playbook
task playbook -- playbooks/ntp.yaml
task playbook -- playbooks/ntp.yaml --check

# Test ansible-role-rpi
task test-role-check  # Dry-run
task test-role        # Apply

# Ad-hoc commands
ansible -i docker-inventory all -a "uptime"
ansible -i docker-inventory all -a "df -h"
ansible -i docker-inventory app_servers -a "hostname"
```
