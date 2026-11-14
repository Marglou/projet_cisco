# projet_cisco - basecode Ansible lab

Minimal Ansible basecode for WSL2/VS Code automation of Ubuntu 22.04 VirtualBox VMs,
with Docker Compose services split into simple roles.

## Basecode layout

- Single inventory file: `inventory/inventory.yml`
- Roles keep only `defaults/main.yml` and `tasks/main.yml` (no role `meta/` files)
- Service roles requested in this basecode:
  - `ssh`
  - `docker_compose`
  - `nginx`
  - `grafana`
  - `prometheus`
  - `mariadb`
  - `dvwa`
- VM lifecycle role: `vbox_vm`

## Quick start

```bash
ansible-galaxy collection install -r requirements.yml
ansible-playbook playbooks/00_create_vm.yml
ansible-playbook playbooks/site.yml --ask-vault-pass
```

## Inventory

Edit `inventory/inventory.yml` and place hosts into groups (`docker_hosts`, `nginx`,
`grafana`, `prometheus`, `mariadb`, `dvwa`, `ssh`, `new_vms`).

## SSH key

Never commit private keys.

```bash
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_ansible
```

Private key path is configured in `inventory/group_vars/all.yml`.

## Template VM preparation

Before creating a `base` snapshot, install in the template VM:

- `openssh-server`
- `python3`
- `sudo`
- `virtualbox-guest-utils`

Ensure your Ansible user has sudo NOPASSWD and your public key in
`~/.ssh/authorized_keys`.

## Vault

```bash
cp inventory/group_vars/monitoring.vault.example inventory/group_vars/monitoring.vault
ansible-vault encrypt inventory/group_vars/monitoring.vault
```

Set `vault_grafana_admin_password` in the vault file.

## WSL2 networking tip

If VM IPs are unreachable from WSL2, enable mirrored mode in `.wslconfig` and restart
WSL.
