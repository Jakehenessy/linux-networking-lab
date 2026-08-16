# Hysteria 2 Server on Ubuntu

## Overview

I deployed and maintained a Hysteria 2 server on an Ubuntu VPS for personal use.

The project included Linux server administration, domain and DNS configuration, TLS certificates, authentication, HTTP/3 masquerading, systemd service management and client connectivity troubleshooting.

The server was configured to provide encrypted connectivity for Windows and Android client devices.

---

## Architecture

```text
Windows / Android Clients
          |
          | Hysteria 2
          | QUIC / UDP 443
          v
      Ubuntu VPS
          |
          +--- Hysteria 2 Server
          |
          +--- TLS Certificate
          |
          +--- Domain / DNS
          |
          +--- systemd
          |
          v
       Internet
```

---

## What I Configured

- Ubuntu Server 24.04
- KVM VPS/VDS
- SSH administration
- Domain and DNS A record
- Hysteria 2 server
- UDP port 443
- TLS certificates
- Let's Encrypt
- Certbot
- Password authentication
- HTTP/3 masquerading
- Salamander traffic obfuscation
- systemd service management
- Windows client connectivity
- Android client connectivity
- Network socket diagnostics
- Service and configuration troubleshooting

---

## Server Deployment

Hysteria 2 was deployed on a Linux VPS and configured as a system service.

The server listened for Hysteria connections on UDP port `443`.

Conceptually:

```text
Client
  |
  | UDP / QUIC
  v
VPS:443
  |
  v
Hysteria 2 Server
```

Using UDP port `443` allowed the service to operate on the standard HTTP/3 port.

---

## Domain and DNS

A dedicated domain was pointed to the VPS using a DNS A record.

The resulting architecture was:

```text
Domain
  |
  | DNS A record
  v
VPS Public IPv4
  |
  v
Hysteria 2
```

This allowed clients to connect using a domain name instead of directly using the server IP address.

---

## TLS

TLS was configured using a Let's Encrypt certificate.

The certificate was obtained with Certbot and stored on the server.

Hysteria referenced the certificate and private key using file paths in its configuration.

Conceptually:

```text
Let's Encrypt
      |
      v
TLS Certificate
      |
      v
Ubuntu VPS
      |
      v
Hysteria 2
```

The production certificate and private key are not included in this repository.

---

## Authentication

The server used password-based authentication.

A client had to provide the correct authentication value before being allowed to use the server.

The production password is intentionally excluded from this repository.

The example configuration uses a placeholder value instead.

## Traffic Obfuscation

The server used Hysteria 2's Salamander obfuscation layer.

Both the server and authorized clients were configured with the same separate obfuscation password.

Conceptually:

```text
Hysteria 2 traffic
        |
        v
Salamander obfuscation
        |
        v
UDP transport
```

The production obfuscation password is intentionally excluded from this repository.

This gave me practical experience with configuring and troubleshooting an additional transport obfuscation layer on both the server and client sides.

---

## HTTP/3 Masquerading

Hysteria was configured with HTTP/3 masquerading using reverse proxy mode.

Instead of returning a proxy-specific response to ordinary HTTP/3 requests, the server could respond with content proxied from a regular website.

The configuration used a structure similar to:

```text
Incoming HTTP/3 request
          |
          v
     Hysteria 2
          |
          v
  Reverse Proxy Mode
          |
          v
 Regular Web Content
```

This was configured using Hysteria's `masquerade` feature.

---

## systemd Service Management

The Hysteria server was managed as a Linux system service.

This allowed the service to start automatically and be controlled through standard systemd tools.

Typical administrative operations included:

```bash
systemctl status hysteria-server.service
```

```bash
systemctl restart hysteria-server.service
```

```bash
journalctl -u hysteria-server.service
```

This provided a standard way to monitor the service, restart it after configuration changes and inspect runtime errors.

---

## Network Diagnostics

During deployment and troubleshooting, I verified whether Hysteria was listening on the expected UDP port.

For example:

```bash
ss -lunp
```

This allowed me to inspect active UDP sockets and confirm that the service was bound to the expected port.

The troubleshooting workflow included checking:

1. Service status
2. Server logs
3. UDP listener
4. DNS resolution
5. TLS configuration
6. Authentication configuration
7. Client configuration
8. Actual client connectivity

---

## Client Devices

The server was used with multiple client devices.

Clients included:

- Windows
- Android

The client applications connected to the Hysteria 2 server using the configured domain, TLS and authentication credentials.

This also gave me experience troubleshooting cases where the server service itself was running correctly but a client could still not establish a connection.

---

## Configuration Management

The Hysteria server configuration was maintained as a YAML file.

The production configuration contained:

- UDP listener
- TLS certificate path
- TLS private key path
- Authentication settings
- Masquerade configuration

The repository contains only a sanitized example.

Real passwords, private keys and production infrastructure information are excluded.

---

## Troubleshooting Approach

When connectivity problems occurred, I tried to isolate the problem instead of changing multiple settings at once.

The general troubleshooting flow was:

```text
Client cannot connect
        |
        v
Check service status
        |
        v
Check server logs
        |
        v
Verify UDP listener
        |
        v
Check DNS
        |
        v
Check TLS
        |
        v
Check authentication
        |
        v
Check client configuration
        |
        v
Test connection again
```

This approach helped determine whether the issue existed on the client, network or server side.

---

## Result

The final setup provided working Hysteria 2 connectivity from Windows and Android devices through my own Ubuntu VPS.

The server used:

- A custom domain
- DNS
- UDP port 443
- TLS certificates
- Password authentication
- HTTP/3 masquerading
- systemd service management

The service was configured to run automatically and could be monitored and troubleshot using standard Linux tools.

---

## Skills Practiced

This project gave me practical experience with:

- Linux server administration
- Ubuntu Server
- VPS/VDS
- SSH
- systemd
- DNS
- UDP
- QUIC
- HTTP/3
- TLS
- Let's Encrypt
- Certbot
- YAML configuration
- Network sockets
- Service logs
- Client/server troubleshooting
- Hysteria 2

---

## Repository Files

- `config.yaml.example` — sanitized Hysteria 2 server configuration
- `operations.md` — service management and troubleshooting notes

---

## Security

This repository contains sanitized documentation and configuration examples only.

The following production information is intentionally excluded:

- VPS public IP address
- Real domain name
- Authentication password
- TLS private key
- SSH keys
- Server credentials
- Active connection URI
- Any other production secrets
