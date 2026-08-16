# Dynamic IP Allowlist Automation

## Overview

The Dante SOCKS5 proxy was configured to accept connections only from explicitly allowed public IPv4 addresses.

This provided a simple access-control mechanism, but introduced an operational problem: the authorized client computers used Internet connections with dynamic public IP addresses.

When an ISP changed a client's public IP address, the existing `/32` rule in the Dante configuration no longer matched that client.

Instead of manually editing the server configuration after every IP change, I implemented an automated client-to-server update mechanism.

---

## The Problem

The initial access-control model looked like this:

```text
Authorized client IP
        |
        v
203.0.113.10/32
        |
        v
Dante allowlist
        |
        v
SOCKS5 Proxy
```

As long as the public IP remained unchanged, the client could connect normally.

However, after an ISP-side address change:

```text
Old public IP: 203.0.113.10
New public IP: 203.0.113.55
```

the Dante rule still contained:

```text
203.0.113.10/32
```

and the client could no longer access the SOCKS5 proxy.

---

## Manual Workflow

Without automation, every public IP change would require the following process:

```text
Public IP changes
        |
        v
Determine the new public IP manually
        |
        v
Connect to VPS through SSH
        |
        v
Edit /etc/danted.conf
        |
        v
Find the correct client rule
        |
        v
Replace the old /32 address
        |
        v
Validate the configuration
        |
        v
Apply the configuration
```

This approach worked, but it was inconvenient and required direct server administration every time a client received a new address.

---

## Automated Solution

I replaced the manual workflow with an automated client-to-server update process.

The final logic was:

```text
Windows Client
      |
      | Detect current public IPv4 address
      v
Client-side update task
      |
      | SSH
      v
Ubuntu VPS
      |
      | Identify corresponding client
      v
Server-side update logic
      |
      | Replace only that client's IP
      v
Dante configuration
      |
      | Validate / apply configuration
      v
Updated SOCKS5 allowlist
```

This allowed authorized clients to maintain access even when their public IP addresses changed.

---

## Multiple Client Support

The proxy was used by more than one authorized client.

Each client therefore needed to be updated independently.

Separate identifiable configuration sections were used:

```text
AUTO-MY-PC
AUTO-FRIEND-PC
```

Conceptually, the Dante configuration contained independent rules like:

```text
AUTO-MY-PC
203.0.113.10/32

AUTO-FRIEND-PC
203.0.113.20/32
```

If the public IP of `AUTO-MY-PC` changed, only the address belonging to that client was updated.

The rule belonging to `AUTO-FRIEND-PC` remained unchanged.

This prevented one client's automated update from overwriting another client's access rule.

---

## Client-Side Logic

The Windows client was responsible for determining its current public IPv4 address and sending the information to the VPS.

The workflow was conceptually:

```text
Start scheduled task
        |
        v
Determine current public IP
        |
        v
Connect to VPS through SSH
        |
        v
Send/update current client IP
        |
        v
Server processes the update
```

The process could be triggered automatically through Windows Task Scheduler instead of requiring the user to perform the update manually.

This made the update mechanism independent of manual server administration.

---

## Server-Side Logic

The server handled the update received from an authorized client.

The server-side process followed this logic:

```text
Receive IP update
        |
        v
Determine which client submitted it
        |
        v
Validate the received IPv4 address
        |
        v
Locate the corresponding configuration section
        |
        v
Replace the previous /32 address
        |
        v
Validate the Dante configuration
        |
        v
Apply the updated configuration
```

Only the configuration entry associated with the corresponding client was modified.

---

## SSH-Based Update Flow

SSH was used as the communication mechanism between the Windows clients and the Ubuntu VPS.

Separate server-side access could be used for different updating clients so that the automation did not require exposing unrestricted administrative access.

The basic communication path was:

```text
Windows PC
    |
    | SSH
    v
Restricted update account / update mechanism
    |
    v
Ubuntu VPS
    |
    v
Dante allowlist update
```

This allowed the update operation to be performed remotely while keeping the SOCKS5 service itself restricted by source IP.

---

## Access-Control Model

The SOCKS5 proxy was not configured as an unrestricted public proxy.

Access was controlled through source IP rules.

Example sanitized rule:

```text
203.0.113.10/32
```

A `/32` IPv4 prefix represents one individual IPv4 address.

Therefore each client could be explicitly authorized without permitting an entire external subnet.

Example:

```text
Client A:
203.0.113.10/32

Client B:
203.0.113.20/32
```

The IP addresses shown in this repository are documentation-only examples.

They are not the real addresses used by the infrastructure.

---

## Failure Scenario

One of the important cases to consider was an invalid or incomplete update.

A configuration automation mechanism should not blindly apply arbitrary input.

The intended safe workflow is:

```text
Receive value
      |
      v
Check that value is a valid IPv4 address
      |
      +---- invalid ----> Reject update
      |
      v
Update configuration
      |
      v
Validate configuration
      |
      +---- invalid ----> Do not apply broken configuration
      |
      v
Apply changes
```

This reduces the chance of an automated update breaking the proxy configuration.

---

## Troubleshooting

When an authorized client could not connect, the troubleshooting process included checking:

1. The client's current public IPv4 address.
2. Whether that IP matched the address stored in the Dante allowlist.
3. Whether the automated update task had executed.
4. SSH connectivity between the client and VPS.
5. The Dante configuration.
6. The Dante service status.
7. Listening sockets and TCP port `1080`.
8. Network connectivity from the client.

Useful Linux-side tools included:

```bash
systemctl status danted
```

and:

```bash
ss -lntp
```

The configuration file could also be inspected to confirm that the correct client address had been inserted.

---

## Result

The final setup allowed the Dante SOCKS5 proxy to remain protected by source-IP allowlisting while supporting authorized clients with dynamic public IP addresses.

Instead of manually connecting to the VPS and modifying `/etc/danted.conf` every time an ISP changed an address, the client-to-server update mechanism maintained the appropriate allowlist entries automatically.

The project combined:

- Linux server administration
- Network access control
- SOCKS5 proxy configuration
- Dynamic IP handling
- SSH
- Windows/Linux interaction
- Configuration management
- Basic infrastructure automation
- Service troubleshooting

---

## What I Learned

This project gave me practical experience with solving an operational infrastructure problem rather than only deploying a service.

The main challenge was not installing Dante itself, but maintaining strict IP-based access control in an environment where authorized client addresses could change.

The solution required coordinating client-side automation with server-side configuration management and ensuring that separate clients could be updated independently.

---

## Security Notes

This repository contains documentation and sanitized configuration examples only.

The following information is intentionally excluded:

- Real VPS IP address
- Real client IP addresses
- SSH private keys
- SSH passwords
- Production credentials
- Real usernames used for automated access
- Any other active authentication secrets
