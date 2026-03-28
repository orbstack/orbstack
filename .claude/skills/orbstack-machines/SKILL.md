---
name: orbstack-machines
description: >-
  Guide for interacting with OrbStack Linux machines via `orb` CLI and SSH.
  Use when the user needs to: (1) create, manage, or configure OrbStack Linux
  machines, (2) run commands or scripts inside Linux machines, (3) SSH into
  machines or set up remote development, (4) transfer files between Mac and
  Linux, (5) configure networking, port forwarding, or proxies for machines,
  (6) set up dev environments with specific distros, (7) automate machine
  provisioning with cloud-init, (8) debug machine connectivity or boot issues.
  Triggers on: "orb", "orbstack", "linux machine", "orb create", "orb ssh",
  "ssh orb", "orb push", "orb pull", "cloud-init orbstack".
---

# OrbStack Machines — `orb` CLI & SSH Guide

OrbStack runs lightweight Linux machines (like WSL) on macOS. Machines start in ~2 seconds, use minimal resources, and integrate deeply with macOS (file sharing, networking, clipboard, SSH agent).

## Quick Reference

- **CLI reference & all commands**: See [references/cli-reference.md](references/cli-reference.md)
- **Networking, SSH & file sharing**: See [references/networking-and-ssh.md](references/networking-and-ssh.md)

## Core Workflow

### 1. Create a Machine

```bash
orb create ubuntu my-dev          # Latest Ubuntu
orb create ubuntu:jammy my-dev    # Specific version
orb create --arch amd64 ubuntu x86-dev   # Intel machine on Apple Silicon
orb create -c cloud-init.yml ubuntu auto-dev  # With cloud-init
```

### 2. Run Commands

```bash
orb                              # Shell in default machine
orb -m my-dev                    # Shell in specific machine
orb -m my-dev ls /etc            # Run single command
orb -m my-dev -u root apt update # Run as root
orb -m my-dev ./setup.sh         # Run a script
```

### 3. SSH Access

```bash
ssh orb                          # Default machine
ssh my-dev@orb                   # Specific machine
ssh user@my-dev@orb              # Specific user
```

VS Code: install Remote-SSH extension, connect to host `orb`.

### 4. File Transfer

```bash
orb push ./local-file.txt /home/user/   # Mac -> Linux
orb pull /var/log/syslog ./logs/        # Linux -> Mac
```

Or use shared paths:
- Mac files in Linux: `/Users/<you>/...` or `/mnt/mac/...`
- Linux files on Mac: `~/OrbStack/<machine>/`

## Sample Use Cases & Prompt Templates

### Dev Environment Setup

> "Create an Ubuntu machine named `webdev`, install Node.js 20 and PostgreSQL, then verify both are running."

```bash
orb create ubuntu webdev
orb -m webdev bash -c "sudo apt update && sudo apt install -y nodejs npm postgresql"
orb -m webdev node --version
orb -m webdev sudo systemctl start postgresql
orb -m webdev sudo systemctl status postgresql
```

### Multi-Distro Testing

> "I need to test my script on Ubuntu, Fedora, and Alpine. Create machines, push my script, run it on each, and collect output."

```bash
orb create ubuntu test-ubuntu
orb create fedora test-fedora
orb create alpine test-alpine

for m in test-ubuntu test-fedora test-alpine; do
  orb push -m "$m" ./my-script.sh /tmp/
  orb -m "$m" bash /tmp/my-script.sh > "output-${m}.log" 2>&1
done
```

### Cloud-Init Automated Provisioning

> "Create a machine with cloud-init that sets up a dev user, installs git and docker, and writes a welcome message."

Write `cloud-init.yml`:
```yaml
#cloud-config
users:
  - name: dev
    sudo: ALL=(ALL) NOPASSWD:ALL
    shell: /bin/bash

packages:
  - git
  - docker.io

write_files:
  - path: /etc/motd
    content: |
      Welcome to the dev environment!
```

```bash
orb create -c cloud-init.yml ubuntu auto-dev
orb -m auto-dev -u dev git --version
```

### SSH Remote Development (VS Code)

> "Set up a Debian machine for remote development with VS Code."

```bash
orb create debian dev-remote
# In VS Code: Remote-SSH -> Connect to Host -> dev-remote@orb
# Working directory: open /home/<user>/project
```

Connection details for JetBrains/other IDEs:
- Host: `localhost`, Port: `32222`
- User: `default` (or `<user>@<machine>`)
- Key: `~/.orbstack/ssh/id_ed25519`

### Service Development & Networking

> "Run nginx in a Linux machine and access it from my Mac browser."

```bash
orb create ubuntu web-server
orb -m web-server bash -c "sudo apt update && sudo apt install -y nginx"
orb -m web-server sudo systemctl start nginx
# Access from Mac: http://localhost or http://web-server.orb.local
```

### Cross-Machine Communication

> "Set up a frontend machine and a backend machine that talk to each other."

```bash
orb create ubuntu frontend
orb create ubuntu backend

# Get backend IP
orb -m backend ip -4 addr show eth0

# From frontend, connect to backend via IP or hostname
orb -m frontend curl http://backend.orb.local:3000/api
```

### Accessing Mac Services from Linux

> "My Mac runs a dev server on port 5000. Access it from the Linux machine."

```bash
orb -m my-dev curl http://host.orb.internal:5000
```

### Intel/x86 Binary Testing on Apple Silicon

> "Test an x86 binary on Apple Silicon Mac."

```bash
orb create --arch amd64 ubuntu x86-test
orb push -m x86-test ./my-x86-binary /tmp/
orb -m x86-test /tmp/my-x86-binary
```

### Debug Machine Issues

> "My machine won't start / services are failing."

```bash
orb logs <machine>               # Check boot logs
orb -m <machine> systemctl status <service>
orb -m <machine> journalctl -xe  # Check system journal
orb restart <machine>            # Restart machine
```

### Ansible Inventory with OrbStack

> "Use Ansible to manage OrbStack machines."

Inventory file:
```ini
[webservers]
ubuntu@orb ansible_user=ubuntu

[dbservers]
debian@orb ansible_user=admin
```

```bash
ansible -i inventory.ini webservers -m ping
```

### Environment Variable Forwarding

> "Pass AWS credentials to a command in the machine."

```bash
ORBENV=AWS_ACCESS_KEY_ID:AWS_SECRET_ACCESS_KEY orb -m my-dev aws s3 ls
# Or export for all commands:
export ORBENV=EDITOR:AWS_ACCESS_KEY_ID:AWS_SECRET_ACCESS_KEY
```

### Bulk Machine Cleanup

> "Delete all test machines."

```bash
orb list
orb delete test-ubuntu
orb delete test-fedora
orb delete test-alpine
```
