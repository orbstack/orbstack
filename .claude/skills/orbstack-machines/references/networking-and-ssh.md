# OrbStack Networking & SSH Reference

## SSH Access

OrbStack provides a built-in SSH server multiplexing access to all machines on port 32222.

### SSH Commands

```bash
ssh orb                        # Default machine, default user
ssh machine@orb                # Specific machine
ssh user@orb                   # Specific user
ssh user@machine@orb           # Specific user + machine
```

### Connection Details (for non-OpenSSH clients)

- **Host:** localhost
- **Port:** 32222
- **User:** default (or `user@machine`)
- **Private key:** ~/.orbstack/ssh/id_ed25519
- **Auth:** Public key only (no password)

### Key Management

- OrbStack generates ED25519 keypair at `~/.orbstack/ssh/`
- Replace or symlink keys, then restart OrbStack
- Add authorized keys: edit `~/.orbstack/ssh/authorized_keys` (ECDSA, RSA supported)
- Symlink to `~/.ssh/authorized_keys` to reuse Mac keys

### Agent Forwarding

SSH agent is automatically forwarded — no configuration needed. Git push, SSH tunnels work out of the box.

### Port Forwarding via SSH

```bash
ssh -L 8080:localhost:80 orb           # Local forward
ssh -R 9090:localhost:3000 orb         # Remote forward
```

Typically unnecessary since Linux services are already on localhost.

### Remote Access

SSH restricted to localhost. For external access: use SSH port forwarding or install a custom SSH server in the machine.

## Networking

### Domain Names

Each machine gets: `machine-name.orb.local` (automatic, no config needed).

### IP Addresses

- IPv4 and IPv6 on `eth0`
- Discover: `ip addr` inside machine

### Port Accessibility

- Linux services are accessible from Mac on `localhost` automatically
- Services on `0.0.0.0` or `::` are accessible from other network devices

### Special Hostnames

| Hostname | Purpose |
|----------|---------|
| `host.orb.internal` | Access macOS services from Linux |
| `docker.orb.internal` | Access Docker container ports from Linux |
| `machine-name.orb.local` | Access machine by name |

### Inter-Machine

All machines share a network bridge. Communicate via IP. ~115 Gbps throughput on M1.

### VPN/Proxy/Certs

- VPN: fully compatible, automatic DNS resolver
- Proxy: inherits macOS proxy (HTTP, HTTPS, SOCKS5)
- Certs: trusts macOS keychain certificates

## File Sharing

### Mac files from Linux

```
/Users/foo/Downloads          # Direct macOS paths work
/mnt/mac/Users/foo/Downloads  # Explicit prefix also works
```

### Linux files from Mac

- Finder: OrbStack tab
- Terminal: `~/OrbStack/<machine>/`

### Inter-Machine files

```
/mnt/machines/<other-machine>/
```

## Running Mac Commands from Linux

```bash
mac <command>                  # Run any macOS command
open foo.txt                   # Open in Mac default app
pbcopy / pbpaste               # Mac clipboard access
mac link <tool>                # Link macOS CLI tool for direct use
```
