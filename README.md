# admin5-server-init

Ansible playbook for basic Linux server initialization.

## What it does

- Creates an admin user with SSH key access and passwordless sudo
- Sets root and admin passwords (optional, vault-encrypted)
- Hardens SSH: disables root login and password authentication
- Installs and configures fail2ban (SSH jail)
- Adds additional users with SSH keys and optional sudo
- Installs Docker from official repos and configures daemon (log rotation, live-restore)

## Requirements

- Python 3.14+
- [uv](https://github.com/astral-sh/uv)

## Quick start

```bash
uv sync
cp inventory/example.hosts.yml inventory/hosts.yml
mkdir -p group_vars/all
cp group_vars/all.yml.example group_vars/all/vars.yml
```

Create vault file for secrets:

```bash
echo 'vault_root_password: "YOUR_ROOT_PASSWORD"' > group_vars/all/vault.yml
echo 'YOUR_VAULT_PASSWORD' > .vault_password
ansible-vault encrypt group_vars/all/vault.yml
```

## Variables

### group_vars/all/vars.yml

| Variable | Default | Description |
|---|---|---|
| `admin_user` | `admin` | Admin username |
| `admin_user_ssh_public_key` | — | SSH public key for admin |
| `root_password` | — | Root password (references vault) |
| `admin_password` | — | Admin user password (references vault) |
| `additional_users` | `[]` | List of `{username, public_key, sudo}` |
| `sshd_port` | `22` | SSH port |
| `fail2ban_bantime` | `3600` | Ban duration in seconds |
| `fail2ban_findtime` | `600` | Time window for counting failures |
| `fail2ban_maxretry` | `5` | Max failures before ban |
| `docker_users` | `[admin_user]` + sudo users | Users added to the `docker` group |
| `docker_log_max_size` | `10m` | Max size of a container log file |
| `docker_log_max_file` | `3` | Max number of rotated log files per container |

Docker variables are commented out in `all.yml.example` — they have sensible defaults and only need to be uncommented if you want to override them.

### group_vars/all/vault.yml (encrypted)

| Variable | Description |
|---|---|
| `vault_root_password` | Root password (plain text, encrypted at rest) |
| `vault_admin_password` | Admin user password (optional) |

### inventory/hosts.yml (per host)

| Variable | Default | Description |
|---|---|---|
| `use_root_login` | `false` | Set to `true` for the first run — connects as root to create the admin user. Set to `false` after the first run, root SSH access will be disabled. |


Fill in your values and run:

```bash
uv run ansible-playbook site.yml
```
