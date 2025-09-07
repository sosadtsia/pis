# pis
Raspberry PI management with Ansible

## Prerequisites

### SSH Key Setup for Initial Pi Configuration

Before running the Ansible playbooks, you need to set up SSH key authentication on your Raspberry Pi. This eliminates the need for password authentication and allows Ansible to connect securely.

#### Method 1: Using `userconf.txt` (Recommended for Raspberry Pi OS Lite)

1. **Generate SSH key pair** (if you don't have one):
   ```bash
   ssh-keygen -t ed25519 -C "your-email@example.com"
   ```

2. **Create user configuration file** on the SD card boot partition:
   ```bash
   # Mount the SD card and navigate to the boot partition
   cd /path/to/sd-card/boot

   # Create userconf.txt with username:encrypted_password
   echo 'pi:$6$your_encrypted_password_here' > userconf.txt
   ```

   To generate the encrypted password:
   ```bash
   echo 'your_password' | openssl passwd -6 -stdin
   ```

3. **Enable SSH** by creating an empty SSH file:
   ```bash
   touch ssh
   ```

4. **Add your public key** to the authorized_keys:
   ```bash
   # Create the SSH directory structure
   mkdir -p home/pi/.ssh

   # Copy your public key
   cat ~/.ssh/id_ed25519.pub > home/pi/.ssh/authorized_keys

   # Set proper permissions (will be applied on first boot)
   chmod 700 home/pi/.ssh
   chmod 600 home/pi/.ssh/authorized_keys
   ```

#### Method 2: Using Raspberry Pi Imager (Easiest)

1. **Download and run** [Raspberry Pi Imager](https://www.raspberrypi.com/software/)

2. **Select your OS** and storage device

3. **Click the gear icon** (⚙️) for advanced options

4. **Configure SSH**:
   - Enable SSH
   - Choose "Use public-key authentication only"
   - Paste your public key content (`cat ~/.ssh/id_ed25519.pub`)

5. **Set username and password** (optional, but recommended)

6. **Write the image** to SD card

#### Method 3: Post-Boot SSH Key Copy

If you've already booted your Pi with SSH enabled:

```bash
# Copy your public key to the Pi
ssh-copy-id pi@pi-hostname.local

# Or manually:
cat ~/.ssh/id_ed25519.pub | ssh pi@pi-hostname.local 'mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys'
```

## Running Playbooks

### Development Environment
```bash
cd develop
ansible-playbook -i docker-inventory playbook.yml
```

### Production Environment
```bash
cd production
ansible-playbook -i inventory playbook.yaml
```

### Verify Connectivity
Before running playbooks, test SSH connectivity:
```bash
ansible all -i inventory -m ping
```

## Project Structure

- `develop/` - Development environment with Docker-based testing
- `production/` - Production environment for actual Raspberry Pi devices
- Each environment contains:
  - `playbook.yaml` - Main Ansible playbook
  - `inventory` - Host definitions
  - `ansible.cfg` - Ansible configuration
  - `vars.yaml` - Environment-specific variables

## Troubleshooting

### SSH Connection Issues
- Ensure SSH is enabled on the Pi (`sudo systemctl enable ssh`)
- Check if your Pi is accessible: `ping pi-hostname.local`
- Verify SSH key permissions: `chmod 600 ~/.ssh/id_ed25519`
- Test manual SSH connection: `ssh pi@pi-hostname.local`

### Ansible Issues
- Verify inventory file contains correct hostnames/IPs
- Check Ansible can reach hosts: `ansible all -i inventory -m ping`
- Ensure Python is installed on target systems
