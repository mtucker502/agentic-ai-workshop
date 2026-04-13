# Workshop VM Provisioning with Ansible

This playbook provisions a fresh Ubuntu 22.04 LTS VM to run the Agentic AI Workshop.

## Prerequisites

1. **Control machine** (your laptop):
   - Ansible installed (`pip install ansible` or `brew install ansible`)
   - SSH access to the target VM as a user with sudo privileges

2. **Target VM**:
   - Ubuntu 22.04 LTS
   - SSH server running
   - A user with sudo access (default: `labuser`)

3. **cRPD Docker image** (Juniper proprietary):
   - Obtain `crpd-25.2R1.9.tgz` from Juniper
   - Place it in `ansible/files/crpd-25.2R1.9.tgz`

## Quick Start

```bash
# 1. Edit inventory to point to your VM
vi ansible/inventory.ini

# 2. Place cRPD image tarball
cp /path/to/crpd-25.2R1.9.tgz ansible/files/

# 3. Run the playbook
cd ansible
ansible-playbook -i inventory.ini setup-workshop-vm.yml
```

## What It Sets Up

| Component | Details |
|-----------|---------|
| **User** | `claude` (uid 1001) with Docker and clab_admins groups |
| **Docker** | Docker CE with compose and buildx plugins |
| **ContainerLab** | Latest version from containerlab.dev |
| **Python** | System Python 3.10+ with UV package manager |
| **SSH Keys** | `id_rsa_claude` keypair for container access |
| **Workspace** | Cloned from `JNPRAutomate/agentic-ai-workshop` |
| **Junos MCP** | Cloned from `Juniper/junos-mcp-server` with venv |
| **Linux MCP** | Cloned from `JNPRAutomate/linux-mcp-server` (V1.0 tag) with venv |
| **Docker Images** | `crpd:25.2R1.9` and `alpine:3.22.1` |

## Customization

Edit `inventory.ini` to change the target host:

```ini
[workshop_vm]
your-vm-hostname ansible_user=your_sudo_user ansible_become=yes
```

## Skip cRPD Image Loading

If the cRPD image is already loaded or you want to load it separately:

```bash
ansible-playbook -i inventory.ini setup-workshop-vm.yml --skip-tags crpd
```

## Post-Setup Verification

SSH into the VM as `claude` and verify:

```bash
ssh claude@your-vm
docker ps
clab version
python3 --version
uv --version
ls ~/workspace/ ~/mcps/
```
