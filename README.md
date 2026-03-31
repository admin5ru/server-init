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
```

Copy example files and fill in your values:

```bash
cp inventory/example.hosts.yml inventory/hosts.yml
cp group_vars/all.yml.example group_vars/all.yml
```

Edit `group_vars/all.yml`:

```yaml
admin_user: admin
admin_user_ssh_public_key: "ssh-ed25519 AAAA... you@host"
use_root_login: true

additional_users:
  - username: deployer
    public_key: "ssh-ed25519 AAAA... deployer@host"
    sudo: false
```

First run (as root):

```bash
uv run ansible-playbook site.yml
```

After the first run, set `use_root_login: false` in `group_vars/all.yml`. All subsequent runs will connect as `admin_user`.

## Variables

| Variable | Default | Description |
|---|---|---|
| `admin_user` | `admin` | Admin username |
| `admin_user_ssh_public_key` | — | SSH public key for admin |
| `use_root_login` | `true` | Connect as root (first run) |
| `additional_users` | `[]` | List of `{username, public_key, sudo}` |
| `sshd_port` | `22` | SSH port |
| `fail2ban_bantime` | `3600` | Ban duration in seconds |
| `fail2ban_findtime` | `600` | Time window for counting failures |
| `fail2ban_maxretry` | `5` | Max failures before ban |
