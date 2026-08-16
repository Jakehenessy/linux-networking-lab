# Hysteria 2 Operations and Troubleshooting

## Overview

This document describes the basic operational and troubleshooting workflow I used while maintaining my personal Hysteria 2 server on Ubuntu.

The server was managed through `systemd` and used:

- UDP port 443
- TLS certificates
- Password authentication
- Salamander obfuscation
- Domain-based client connections
- Windows and Android clients

The goal during troubleshooting was to isolate the problem step by step instead of changing multiple configuration parameters at once.

---

## Configuration Location

The Hysteria 2 server configuration was stored at:

```text
/etc/hysteria/config.yaml
```

To edit the configuration:

```bash
sudo nano /etc/hysteria/config.yaml
```

After modifying the configuration, the service was restarted and checked for errors.

---

## Check Service Status

The first step when troubleshooting the server was checking whether the Hysteria service was running:

```bash
systemctl status hysteria-server.service
```

This provides information about:

- Whether the service is active
- Whether it exited with an error
- Recent systemd messages
- Process information
- Startup failures

A healthy server should normally show the service as:

```text
active (running)
```

---

## Restart the Service

After changing the configuration:

```bash
sudo systemctl restart hysteria-server.service
```

Then I checked the service status again:

```bash
systemctl status hysteria-server.service
```

This helped immediately detect configuration errors that prevented Hysteria from starting.

---

## Enable Automatic Startup

The service can be enabled to start automatically with the server:

```bash
sudo systemctl enable hysteria-server.service
```

To enable it and start it immediately:

```bash
sudo systemctl enable --now hysteria-server.service
```

This ensures that Hysteria starts automatically after a VPS reboot.

---

## View Server Logs

When the service failed to start or clients could not connect, I checked the systemd journal.

Recent Hysteria server logs:

```bash
journalctl --no-pager -e -u hysteria-server.service
```

To follow logs in real time:

```bash
journalctl -f -u hysteria-server.service
```

Logs are useful for identifying problems related to:

- Invalid YAML configuration
- TLS certificates
- Authentication
- Obfuscation settings
- Port binding
- Runtime errors
- Client connection attempts

---

## Verify UDP Listener

Hysteria 2 uses QUIC over UDP.

In my setup the service listened on UDP port `443`.

To check active UDP listeners:

```bash
ss -lunp
```

A more focused check:

```bash
ss -lunp | grep ':443'
```

This helps confirm whether a process is actually listening on the expected UDP port.

Conceptually:

```text
Hysteria service
      |
      v
UDP :443 listener
      |
      v
Client connection
```

If the systemd service reports that it is running but there is no expected UDP listener, this indicates that further investigation is required.

---

## Check DNS Resolution

Clients connected using a domain name instead of the raw VPS IP address.

Because of this, DNS was part of the troubleshooting process.

Example:

```bash
getent hosts example.com
```

or:

```bash
nslookup example.com
```

The resolved IP should correspond to the VPS address.

Conceptually:

```text
Domain
   |
   v
DNS resolution
   |
   v
VPS public IP
```

An incorrect DNS record can make the client connect to the wrong server even if Hysteria itself is configured correctly.

---

## TLS Troubleshooting

The server used Let's Encrypt TLS certificates generated with Certbot.

The configuration referenced files similar to:

```text
/etc/letsencrypt/live/example.com/fullchain.pem
/etc/letsencrypt/live/example.com/privkey.pem
```

When troubleshooting TLS, I checked:

1. Whether the certificate files existed.
2. Whether Hysteria could read them.
3. Whether the certificate matched the configured domain.
4. Whether the client was connecting using the correct hostname.

Example file check:

```bash
sudo ls -l /etc/letsencrypt/live/example.com/
```

The real production domain is intentionally excluded from this repository.

---

## Authentication Troubleshooting

The server used password authentication.

If the client reached the server but authentication failed, I checked:

1. Server authentication configuration.
2. Client authentication value.
3. Whether the client was connecting to the correct server.

The production authentication password is not stored in this repository.

---

## Salamander Obfuscation Troubleshooting

Salamander obfuscation was enabled in my Hysteria 2 setup.

Both the client and server need matching obfuscation settings.

The server configuration used a structure similar to:

```yaml
obfs:
  type: salamander
  salamander:
    password: CHANGE_ME_OBFS_PASSWORD
```

When troubleshooting a connection, I checked:

1. Whether obfuscation was enabled on the server.
2. Whether the client also used Salamander.
3. Whether both sides used the same obfuscation password.

A mismatch can prevent the client from establishing a connection even when the server itself is running correctly.

The real obfuscation password is intentionally excluded.

---

## Configuration Validation Workflow

After changing the server configuration, I followed a simple validation process:

```text
Edit config
    |
    v
Restart service
    |
    v
Check systemd status
    |
    v
Read logs
    |
    v
Verify UDP listener
    |
    v
Test client connection
```

This made it easier to identify which change introduced a problem.

---

## Client Connectivity Troubleshooting

When a Windows or Android client could not connect, I checked the system in the following order:

```text
Client cannot connect
        |
        v
Is hysteria-server.service running?
        |
        v
Is UDP 443 listening?
        |
        v
Does the domain resolve correctly?
        |
        v
Is TLS configured correctly?
        |
        v
Do Salamander settings match?
        |
        v
Does authentication match?
        |
        v
Is the client configuration correct?
        |
        v
Test connection again
```

This approach allowed me to narrow the problem down to the client, DNS, network transport or server configuration.

---

## Useful Commands

### Service status

```bash
systemctl status hysteria-server.service
```

### Restart service

```bash
sudo systemctl restart hysteria-server.service
```

### Enable at startup

```bash
sudo systemctl enable --now hysteria-server.service
```

### Recent logs

```bash
journalctl --no-pager -e -u hysteria-server.service
```

### Follow logs

```bash
journalctl -f -u hysteria-server.service
```

### Inspect UDP listeners

```bash
ss -lunp
```

### Check UDP port 443

```bash
ss -lunp | grep ':443'
```

### Edit Hysteria configuration

```bash
sudo nano /etc/hysteria/config.yaml
```

### Check DNS

```bash
getent hosts example.com
```

---

## Problems This Workflow Helps Diagnose

### Service does not start

Possible areas to investigate:

- Invalid YAML configuration
- Incorrect file paths
- TLS certificate permissions
- Port binding problems
- Invalid configuration values

### Service runs but client times out

Check:

- UDP port availability
- Hosting provider firewall
- Server firewall
- DNS resolution
- Server address
- Salamander obfuscation settings
- Client configuration

### Authentication fails

Check:

- Server authentication password
- Client authentication password
- Correct server/domain

### TLS verification fails

Check:

- Certificate validity
- Certificate domain
- Client hostname
- Certificate file configuration

### Service works after restart but not after VPS reboot

Check:

```bash
systemctl is-enabled hysteria-server.service
```

If necessary:

```bash
sudo systemctl enable hysteria-server.service
```

---

## Result

Using this workflow, I could diagnose Hysteria 2 problems from several different layers:

```text
Client
  |
  v
DNS
  |
  v
UDP / Network
  |
  v
Salamander Obfuscation
  |
  v
TLS
  |
  v
Authentication
  |
  v
Hysteria Service
  |
  v
Linux / systemd
```

This project gave me practical experience with service monitoring, Linux networking, configuration troubleshooting and client/server diagnostics.

---

## Security Notes

This repository does not contain:

- Production server IP addresses
- Real domain names
- Authentication passwords
- Salamander obfuscation passwords
- TLS private keys
- SSH credentials
- Active Hysteria connection URIs

All examples are sanitized for public documentation.
