# Hosts

Every machine you run. Prefer the VPN address; LAN IPs move.

| Host | VPN | LAN | User | SSH | Role |
|------|-----|-----|------|-----|------|
| workstation | 100.x.x.x | - | you | local | Daily driver |
| server | 100.x.x.x | 10.0.0.x | you | `ssh server` | Production: sites, containers |
| laptop | 100.x.x.x | 10.0.0.x | you | `ssh you@100.x.x.x` | Portable |

Sudo: describe how it works. If non-interactive sudo is available, give the
exact form.

## Rules

- Which hosts are production and what an agent must never touch there without
  asking (restarting the container runtime, editing system configs, migrating
  databases).
- Auth quirks: lockout policies, key vs password, jump hosts.
- Where the service and port inventory lives, if it is not in this file.
