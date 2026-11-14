# projet_cisco - minimal Ansible infrastructure

This repository contains a minimal, idempotent Ansible project to:
- create Ubuntu 22.04 VirtualBox VMs from a template snapshot,
- apply baseline hardening,
- install Docker Engine and Compose plugin,
- deploy NGINX, Grafana, and Suricata using Docker Compose.

## Quick start

```bash
ansible-galaxy collection install -r requirements.yml
ansible-playbook playbooks/00_create_vms.yml
ansible-playbook playbooks/site.yml --ask-vault-pass
```

## SSH keys

Never commit a private key.

Generate a dedicated key pair outside this repository:

```bash
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_ansible
```

The public key placeholder is at:
- `roles/vbox_vm/files/id_ed25519_ansible.pub`

Set private key path in `inventory/group_vars/all.yml`:
- `ansible_ssh_private_key_file: "~/.ssh/id_ed25519_ansible"`

## Prepare the template VM

Prepare your Ubuntu 22.04 template VM before snapshotting:
- install `openssh-server`, `python3`, `sudo`, `virtualbox-guest-utils`
- create an automation user with sudo NOPASSWD
- append your public key to `~/.ssh/authorized_keys`
- use host-only or bridged networking (not NAT-only)
- create a snapshot named `base`

## Vault secrets

Create your encrypted variable file from the example:

```bash
cp inventory/group_vars/monitoring.vault.example inventory/group_vars/monitoring.vault
ansible-vault encrypt inventory/group_vars/monitoring.vault
```

Expected secret variable:
- `vault_grafana_admin_password`

## VirtualBox VM lifecycle

Role `vbox_vm` supports:
- `vbox_vm_state: present` (clone/start/wait for SSH)
- `vbox_vm_state: absent` (poweroff/delete)

## WSL2 networking note

If VM IPs are unreachable from WSL2, enable mirrored networking mode in your Windows
`.wslconfig`, then restart WSL.
