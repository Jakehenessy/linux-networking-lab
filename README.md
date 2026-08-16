# Linux Networking Lab

Hands-on portfolio based on my personal Linux VPS infrastructure.

This repository contains sanitized examples of infrastructure projects I built and maintained for personal use.

## Projects

### Dante SOCKS Proxy with Dynamic IP Allowlist

A Dante SOCKS5 proxy deployed on Ubuntu Server with access restricted by source IP addresses.

The main challenge was that client machines used dynamic public IP addresses.  
I implemented an automated client-to-server update flow so the allowlist could be updated when a client's public IP changed, without manually editing the Dante configuration.

**Implemented:**
- Hysteria 2 deployment on Linux
- UDP/443 listener
- Domain and DNS configuration
- TLS certificates with Let's Encrypt
- Password authentication
- Salamander traffic obfuscation
- HTTP/3 masquerading
- systemd service management
- Client connections from Windows and Android
- Service, log and socket troubleshooting

[View Dante project](./dante/)

---

### Hysteria 2 Server

Hysteria 2 server deployed on Ubuntu Server for personal use.

**Implemented:**
- Hysteria 2 deployment on Linux
- UDP/443 listener
- Domain and DNS configuration
- TLS certificates with Let's Encrypt
- Password authentication
- HTTP/3 masquerading
- systemd service management
- Client connections from Windows and Android
- Service, log and socket troubleshooting

[View Hysteria 2 project](./hysteria2/)

---

## Technologies

- Linux
- Ubuntu Server
- VPS / VDS
- SSH
- systemd
- TCP/IP
- UDP
- DNS
- HTTP / HTTPS
- TLS
- Let's Encrypt
- Certbot
- Dante
- SOCKS5
- Hysteria 2
- Xray / VLESS Reality
- Docker

## Troubleshooting

For infrastructure problems I usually work from the lowest relevant layer upward:

1. Client configuration
2. Network connectivity
3. DNS resolution
4. Open ports and active sockets
5. Service status
6. Logs
7. Application configuration

Tools and commands I have worked with include:

`systemctl`, `journalctl`, `ss`, SSH and standard Linux CLI utilities.

## Security

All files in this repository are sanitized examples.

Real server IP addresses, passwords, authentication secrets, SSH private keys and TLS private keys are intentionally excluded.

## About

I built these projects while learning Linux administration and networking through practical tasks.

My current goal is to start working in Technical Support / Linux Support and continue developing toward System Administration.
