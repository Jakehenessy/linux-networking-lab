# Linux Networking Lab

Hands-on portfolio based on my personal Linux VPS infrastructure.

This repository contains sanitized examples of infrastructure projects I built and maintained for personal use.

## Projects

### Dante SOCKS Proxy with Dynamic IP Allowlist

A Dante SOCKS5 proxy deployed on Ubuntu Server with access restricted by source public IP addresses.

The main challenge was that client machines used dynamic public IP addresses.

I implemented an automated client-to-server update flow so the allowlist could be updated when a client's public IP changed, without manually editing the Dante configuration.

**Implemented:**
- Dante SOCKS5 proxy deployment
- TCP port 1080
- Source IP-based access control
- Separate allowlist rules for multiple clients
- Dynamic public IP handling
- SSH-based update flow
- Windows-side automation
- Automatic server-side allowlist updates
- systemd service management
- Network and service troubleshooting

[View Dante project](./dante/)

---

### Hysteria 2 Server

Hysteria 2 server deployed on Ubuntu Server for personal use.

The project combines Linux administration, networking, TLS, traffic obfuscation and client/server troubleshooting.

**Implemented:**
- Hysteria 2 deployment on Linux
- QUIC / UDP 443
- Domain and DNS configuration
- TLS certificates with Let's Encrypt
- Password authentication
- Salamander traffic obfuscation
- Masquerade in reverse proxy mode
- systemd service management
- Client connections from Windows and Android
- Service, log and socket troubleshooting

[View Hysteria 2 project](./hysteria2/)

---

## Technologies

### Linux & Infrastructure

- Linux
- Ubuntu Server 24.04
- VPS / VDS
- SSH
- systemd
- Linux CLI
- Docker

### Networking

- TCP/IP
- UDP
- DNS
- HTTP / HTTPS
- TLS
- QUIC
- SOCKS5

### Services & Tools

- Dante
- Hysteria 2
- Xray / VLESS Reality
- Let's Encrypt
- Certbot

### Troubleshooting

- Service status analysis
- Log analysis
- Network socket inspection
- DNS troubleshooting
- Client/server connectivity troubleshooting
- Configuration troubleshooting

Tools and commands I have worked with include:

```bash
systemctl
journalctl
ss
```

---

## Troubleshooting Approach

For infrastructure problems I usually try to isolate the issue step by step rather than changing multiple settings at once.

A typical workflow is:

```text
Client
  |
  v
Network connectivity
  |
  v
DNS
  |
  v
Ports / sockets
  |
  v
Service status
  |
  v
Logs
  |
  v
Application configuration
```

This helps determine whether a problem exists on the client, network or server side.

---

## Projects in This Repository

```text
linux-networking-lab/
│
├── README.md
│
├── dante/
│   ├── README.md
│   ├── danted.conf.example
│   └── dynamic-ip-update.md
│
└── hysteria2/
    ├── README.md
    ├── config.yaml.example
    └── operations.md
```

---

## Security

All files in this repository are sanitized examples.

The repository intentionally excludes:

- Real server IP addresses
- Real client IP addresses
- Production domain names
- Authentication passwords
- Obfuscation passwords
- SSH private keys
- TLS private keys
- Active connection URIs
- Other production credentials

---

## About Me

I built these projects while learning Linux administration and networking through practical tasks on my own infrastructure.

Rather than limiting myself to installation guides, I try to understand how services work, troubleshoot problems across different layers and automate repetitive administration where possible.

My current goal is to start working in **Technical Support / Linux Support** and continue developing toward **System Administration**.
