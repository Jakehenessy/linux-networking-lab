# Dante SOCKS Proxy with Dynamic IP Allowlist

## Overview

I deployed a Dante SOCKS5 proxy on an Ubuntu VPS for personal use.

Access to the proxy was restricted by source public IP addresses.

The main operational problem was that the client computers did not have static public IP addresses. When an ISP changed a client's public IP, the client could no longer connect until the server allowlist was updated.

To avoid manually editing the configuration after every IP change, I implemented an automated IP update flow.

## Architecture

```text
Windows Client
     |
     | Detect current public IP
     v
Automated client task
     |
     | SSH
     v
Ubuntu VPS
     |
     | Update allowed client IP
     v
Dante configuration
     |
     | Apply configuration
     v
SOCKS5 Proxy
```

Two client machines were handled independently:

```text
Client A ----\
              ---> Dante SOCKS5 Proxy ---> Internet
Client B ----/
```

Each client had its own allowed public IP address and its own configuration block.

## What I Configured

- Ubuntu VPS
- Dante SOCKS5 server
- SOCKS listener on TCP port 1080
- External network interface
- Source IP-based access control
- Separate allowlist entries for multiple clients
- SSH access for the IP update mechanism
- Automated client-side public IP detection
- Windows-side scheduled execution
- Automatic server-side allowlist updates
- Linux service management
- Configuration validation
- Network and connectivity troubleshooting

## Dynamic IP Problem

The initial configuration worked while the client public IP addresses remained unchanged.

When an ISP assigned a new public IP address, the existing `/32` allowlist rule no longer matched the client.

The manual workflow would have been:

```text
Determine new public IP
        ↓
Connect to the server through SSH
        ↓
Open /etc/danted.conf
        ↓
Replace the previous IP address
        ↓
Apply the new configuration
```

This was inconvenient because the process had to be repeated every time one of the clients received a new public IP address.

## Automated Solution

I replaced the manual workflow with an automated client-to-server update mechanism:

```text
Detect current public IP automatically
        ↓
Send the current IP to the VPS through SSH
        ↓
Identify the corresponding client
        ↓
Update only that client's allowlist entry
        ↓
Validate and apply the updated configuration
```

Separate configuration sections were used for each client, for example:

```text
AUTO-MY-PC
AUTO-FRIEND-PC
```

This allowed one client's address to be updated without modifying the other client's access rule.

## Access Control

The proxy was not intended to be openly accessible from the Internet.

Dante access rules restricted connections to explicitly allowed source IPv4 addresses.

Each authorized client used an individual `/32` rule.

Example:

```text
203.0.113.10/32
203.0.113.20/32
```

The addresses above are documentation-only examples and are not real production IP addresses.

## Result

The SOCKS5 proxy remained restricted to authorized clients while still supporting client computers with dynamic public IP addresses.

After the automation was configured, an ISP-side IP change no longer required manually connecting to the server and editing `/etc/danted.conf`.

This project gave me practical experience with both Linux service configuration and solving an operational networking problem through automation.

## Skills Practiced

- Linux server administration
- Ubuntu Server
- SSH
- Dante
- SOCKS5
- TCP/IP
- Source IP access control
- Dynamic public IP addressing
- Linux configuration files
- systemd service management
- Windows/Linux interaction
- Basic infrastructure automation
- Network troubleshooting
- Configuration troubleshooting

## Repository Files

- `danted.conf.example` — sanitized example of the Dante configuration
- `dynamic-ip-update.md` — description of the dynamic IP update mechanism

## Security

This repository contains sanitized examples only.

The following information is intentionally excluded:

- Real server IP addresses
- Real client IP addresses
- Passwords
- SSH private keys
- Authentication credentials
- Production server secrets
