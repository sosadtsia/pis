# Docker Raspberry Pi Test Environment

Docker Compose setup with Debian containers for testing Ansible roles against Raspberry Pi-like systems.

## Quick Start

```bash
# Start containers
docker-compose up -d

# Wait for initialization (~40 seconds)
sleep 40

# Test with Ansible
ansible -i docker-inventory all -m ping
```

## Containers

| Name | Hostname | Internal IP | SSH Port | Credentials |
|------|----------|-------------|----------|-------------|
| app1 | pis-app1.test | 192.168.60.4 | 2221 | pidev/pidev |
| app2 | pis-app2.test | 192.168.60.5 | 2222 | pidev/pidev |
| db | pis-db.test | 192.168.60.6 | 2223 | pidev/pidev |

User `pidev` has sudo access with the same password.

## SSH Access

```bash
# Direct SSH
ssh pidev@127.0.0.1 -p 2221  # Password: pidev

# Using sshpass (no prompt)
sshpass -p 'pidev' ssh -p 2221 pidev@127.0.0.1

# Test all containers
for port in 2221 2222 2223; do
  sshpass -p 'pidev' ssh -p $port pidev@127.0.0.1 "hostname"
done
```

## Ansible Usage

### Prerequisites

```bash
# Install sshpass
brew install hudochenkov/sshpass/sshpass

# Install Ansible and collections
pip install ansible
ansible-galaxy collection install community.general ansible.posix
```

### Run Playbooks

```bash
# Test connectivity
ansible -i docker-inventory all -m ping

# Run playbook (check mode)
ansible-playbook -i docker-inventory playbook.yml --check

# Apply changes
ansible-playbook -i docker-inventory playbook.yml

# Run with tags
ansible-playbook -i docker-inventory playbook.yml --tags "packages"
```

### Inventory

The `docker-inventory` file is pre-configured with connection details:

```ini
[app_servers]
app1 ansible_host=127.0.0.1 ansible_port=2221
app2 ansible_host=127.0.0.1 ansible_port=2222

[db_servers]
db ansible_host=127.0.0.1 ansible_port=2223

[all:vars]
ansible_user=pidev
ansible_ssh_pass=pidev
ansible_become_pass=pidev
ansible_python_interpreter=/usr/bin/python3
```

## Common Commands

```bash
# Start/stop
docker-compose up -d
docker-compose stop
docker-compose down

# View logs
docker-compose logs app1

# Check status
docker-compose ps

# Access container shell
docker exec -it develop-app1-1 bash
docker exec -it -u pidev develop-app1-1 bash

# Rebuild
docker-compose down
docker-compose up -d
```
