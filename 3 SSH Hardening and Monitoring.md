## SSH Hardening and Remote Administration Security

After configuring the laptop as a dedicated homelab server, I implemented several security controls to harden SSH access and reduce the risk of unauthorized access.

### Objectives

- Secure remote administration of the server
- Prevent brute-force login attempts
- Monitor authentication activity
- Eliminate password-based authentication
- Restrict access to trusted devices only

### Security Improvements

#### SSH Availability and Reliability

Like stated before, I configured Linux Mint power management settings to prevent the laptop from entering sleep mode while connected to power. This allows the server to remain accessible over SSH 24/7 without requiring physical interaction.

#### Fail2Ban Deployment

Installed and configured Fail2Ban to automatically detect and block brute-force login attempts targeting the SSH service.

Configuration included:

- SSH jail enabled
- Maximum of 5 failed login attempts
- 10-minute detection window
- 1-hour automatic ban duration

This significantly reduces the effectiveness of password-guessing attacks.

#### Authentication Logging

Enabled monitoring of SSH authentication activity through:

```text
/var/log/auth.log
```

This allows visibility into:

- Successful logins
- Failed login attempts
- Invalid user access attempts
- Authentication anomalies

#### SSH Key Authentication

Generated and deployed Ed25519 SSH key pairs to replace password-based authentication.

Benefits:

- Strong cryptographic authentication
- Elimination of password exposure risks
- Access restricted to trusted devices with authorized private keys

Authorized devices currently include:

- Primary desktop workstation
- Homelab server administration devices

#### Password Authentication Disabled

After verifying SSH key functionality, password authentication was disabled within the SSH daemon configuration.

As a result:

- Password-based logins are no longer accepted
- Unauthorized devices cannot connect without an approved SSH key
- Access must be explicitly authorized by adding a public key to the server

### Security Layers Implemented

The server now utilizes multiple layers of protection:

1. **SSH Key Authentication**
   - Restricts access to trusted devices
   - Eliminates password-based attacks

2. **Fail2Ban**
   - Detects and blocks brute-force login attempts
   - Automatically bans malicious IP addresses

3. **Authentication Logging**
   - Provides visibility into access attempts
   - Enables security monitoring and auditing

### Skills Demonstrated

- Linux Administration
- SSH Hardening
- Access Control
- Log Monitoring
- Fail2Ban Configuration
- Security Troubleshooting
- Remote Server Management

### Outcome

Successfully transformed a default SSH installation into a hardened remote administration solution using layered security controls, automated attack prevention, and key-based authentication. This configuration follows common security practices used in enterprise Linux environments and provides a secure foundation for future homelab services.
