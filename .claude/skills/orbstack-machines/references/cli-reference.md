# OrbStack CLI Reference (`orb`)

## Machine Management

| Command | Description |
|---------|-------------|
| `orb create <distro> [name]` | Create a new machine |
| `orb create <distro>:<version> [name]` | Create with specific version |
| `orb create --arch amd64 <distro> [name]` | Create Intel/x86 machine on Apple Silicon |
| `orb create --set-password <distro> [name]` | Create and set user password |
| `orb create -c user-data.yml <distro> [name]` | Create with cloud-init config |
| `orb delete <machine>` | Delete a machine |
| `orb start [machine]` | Start OrbStack or a specific machine |
| `orb stop [machine]` | Stop OrbStack or a specific machine |
| `orb restart <machine>` | Restart a machine |
| `orb rename <old> <new>` | Rename a machine |
| `orb list` | List all machines |
| `orb info <machine>` | Get machine info |
| `orb logs <machine>` | Show boot/console logs |
| `orb status` | Check if OrbStack is running |
| `orb default [machine]` | Get or set default machine |
| `orb version` | Show OrbStack version |
| `orb update` | Update OrbStack |

## Running Commands

| Command | Description |
|---------|-------------|
| `orb` | Open shell in default machine |
| `orb <command>` | Run command in default machine |
| `orb -m <machine>` | Open shell in specific machine |
| `orb -m <machine> <command>` | Run command in specific machine |
| `orb -u <user>` | Run as specific user |
| `orb -u root` | Run as root |
| `orb -m <machine> -u <user> <command>` | Run as user in specific machine |
| `orb sudo <command>` | Run with sudo (passwordless) |

## File Transfer

| Command | Description |
|---------|-------------|
| `orb push <local-path> [remote-path]` | Copy files from Mac to Linux |
| `orb pull <remote-path> [local-path]` | Copy files from Linux to Mac |
| `orb push -m <machine> <path>` | Push to specific machine |
| `orb pull -m <machine> <path>` | Pull from specific machine |

## Network/Proxy Configuration

```bash
orb config set network_proxy http://example.com
orb config set network_proxy https://user:password@example.com:8443
orb config set network_proxy socks5://user:password@example.com:1081
orb config set network_proxy auto       # inherit macOS proxy
orb config set network_proxy none       # disable proxy
```

## Supported Distros

Ubuntu, Debian, Fedora, Arch, Alpine, CentOS, Rocky, Alma, Oracle, openSUSE, Kali, NixOS, Gentoo, Devuan, Void

Default: latest stable version. Specify version: `distro:version` (e.g., `ubuntu:jammy`, `debian:bookworm`, `fedora:36`, `alpine:edge`).
