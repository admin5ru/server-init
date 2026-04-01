# admin5-server-init

Ansible playbook for basic Linux server initialization.

## What it does

- Creates an admin user with SSH key access and passwordless sudo
- Hardens SSH: disables root login and password authentication
- Installs and configures fail2ban (SSH jail)
- Adds additional users with SSH keys and optional sudo

## Requirements

- Python 3.14+
- [uv](https://github.com/astral-sh/uv)

## Quick start

```bash
uv sync
cp inventory/example.hosts.yml inventory/hosts.yml
cp group_vars/all.yml.example group_vars/all.yml
```

## Variables

### group_vars/all.yml

| Variable | Default | Description |
|---|---|---|
| `admin_user` | `admin` | Admin username |
| `admin_user_ssh_public_key` | — | SSH public key for admin |
| `additional_users` | `[]` | List of `{username, public_key, sudo}` |
| `sshd_port` | `22` | SSH port |
| `fail2ban_bantime` | `3600` | Ban duration in seconds |
| `fail2ban_findtime` | `600` | Time window for counting failures |
| `fail2ban_maxretry` | `5` | Max failures before ban |

### inventory/hosts.yml (per host)

| Variable | Default | Description |
|---|---|---|
| `use_root_login` | `false` | Set to `true` for the first run — connects as root to create the admin user. Set to `false` after the first run, root SSH access will be disabled. |


Fill in your values and run:

```bash
uv run ansible-playbook site.yml
```
