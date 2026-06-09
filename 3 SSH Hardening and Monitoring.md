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

# Commands Used

## 1. System Updates

Update package repositories:

```bash
sudo apt update
```

Upgrade installed packages:

```bash
sudo apt upgrade -y
```

Reboot system:

```bash
sudo reboot
```

---

## 2. Install and Configure SSH

Install OpenSSH Server:

```bash
sudo apt install openssh-server -y
```

Enable SSH at boot and start service:

```bash
sudo systemctl enable --now ssh
```

Verify SSH status:

```bash
sudo systemctl status ssh
```

Check current username:

```bash
whoami
```

Find server IP address:

```bash
hostname -I
```

---

## 3. Configure Firewall (UFW)

Install UFW:

```bash
sudo apt install ufw -y
```

Allow SSH through firewall:

```bash
sudo ufw allow ssh
```

Enable firewall:

```bash
sudo ufw enable
```

Verify firewall status:

```bash
sudo ufw status
```

---

## 4. Monitor SSH Authentication Logs

View live authentication logs:

```bash
sudo tail -f /var/log/auth.log
```

View failed login attempts:

```bash
sudo grep "Failed password" /var/log/auth.log
```

View successful logins:

```bash
sudo grep "Accepted" /var/log/auth.log
```

---

## 5. Install Fail2Ban

Install Fail2Ban:

```bash
sudo apt install fail2ban -y
```

Enable and start Fail2Ban:

```bash
sudo systemctl enable --now fail2ban
```

Check service status:

```bash
sudo systemctl status fail2ban
```

---

## 6. Configure Fail2Ban

Create editable configuration:

```bash
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
```

Edit configuration:

```bash
sudo nano /etc/fail2ban/jail.local
```

Configure SSH protection:

```ini
[sshd]
enabled = true
maxretry = 5
findtime = 10m
bantime = 1h
```

Restart Fail2Ban:

```bash
sudo systemctl restart fail2ban
```

Verify Fail2Ban:

```bash
sudo fail2ban-client status
```

Verify SSH jail:

```bash
sudo fail2ban-client status sshd
```

---

## 7. Troubleshooting Fail2Ban

Check service status:

```bash
sudo systemctl status fail2ban
```

Review service logs:

```bash
sudo journalctl -u fail2ban --no-pager -n 50
```

Validate configuration syntax:

```bash
sudo fail2ban-client -t
```

Check Fail2Ban log:

```bash
sudo tail -50 /var/log/fail2ban.log
```

---

## 8. Generate SSH Keys

Generate Ed25519 key pair on client machine:

```bash
ssh-keygen -t ed25519
```

Verify key files:

### Windows

```powershell
dir $env:USERPROFILE\.ssh
```

### Linux

```bash
ls ~/.ssh
```

---

## 9. Manual Key Installation

Create SSH directory:

```bash
mkdir -p ~/.ssh
```

Set permissions:

```bash
chmod 700 ~/.ssh
```

Create authorized keys file:

```bash
nano ~/.ssh/authorized_keys
```

Set permissions:

```bash
chmod 600 ~/.ssh/authorized_keys
```

---

## 10. Disable Password Authentication

Edit SSH configuration:

```bash
sudo nano /etc/ssh/sshd_config
```

Modify:

```ini
PasswordAuthentication no
PermitRootLogin no
PubkeyAuthentication yes
```

Restart SSH:

```bash
sudo systemctl restart ssh
```

---

## 11. Verify Key-Based Authentication

Connect to server:

```bash
ssh aidan@192.168.1.50
```

Monitor logs:

```bash
sudo tail -f /var/log/auth.log
```

Successful key authentication appears as:

```text
Accepted publickey for aidan
```

---

## Security Outcome

Implemented layered SSH security through:

- UFW Firewall
- SSH Key Authentication
- Fail2Ban Brute-Force Protection
- Authentication Logging
- Disabled Password Authentication
- Restricted Remote Administration Access
