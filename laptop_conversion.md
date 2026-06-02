## Converting the Laptop into a Home Server (SSH Setup)

After successfully installing Linux Mint and wiping the system, I began configuring the laptop to function as a lightweight home server that I could access remotely over my local network using SSH.

The goal was to be able to securely access the machine from my main desktop without needing to physically interact with it.

---

## Installing and Enabling SSH

To set up SSH, I installed and enabled the OpenSSH server:

```bash
sudo apt update
sudo apt install openssh-server -y
sudo systemctl enable --now ssh
systemctl status ssh
```

### What these commands do:

- `sudo apt update` → Updates package lists to ensure everything is current
- `sudo apt install openssh-server -y` → Installs the SSH server
- `sudo systemctl enable --now ssh` → Starts the SSH service and enables it on boot
- `systemctl status ssh` → Confirms the service is running correctly

---

## Connecting to the Server

To establish a connection, I gathered the required system information:

```bash
whoami
hostname -I
```

- `whoami` → Displays the current username
- `hostname -I` → Shows the local IP address of the machine

From my main desktop, I then connected using:

```bash
ssh <user>@<ip>
```

This allowed me to remotely access the Linux Mint system over my home network.

---

## Troubleshooting SSH Access

Initially, SSH connections failed. After some investigation, I discovered that the firewall was blocking incoming SSH traffic.

To resolve this, I allowed SSH through the firewall:

```bash
sudo ufw allow ssh
```

After applying this rule, SSH access worked as expected.

---

## Preventing Sleep and Lid Closure Suspension

To ensure the machine could operate as a persistent server, I needed to prevent it from sleeping or suspending when idle or when the laptop lid is closed.

I first checked the current system sleep behavior:

```bash
systemctl status sleep.target
systemctl status suspend.target
```

These confirmed that the system would enter sleep mode under certain conditions, including lid closure.

---

## Disabling Sleep and Lid Suspend Behavior

To change this behavior, I edited the system logind configuration file:

```bash
sudo nano /etc/systemd/logind.conf
```

I updated the following settings:

```ini
HandleLidSwitch=ignore
HandleLidSwitchExternalPower=ignore
HandleLidSwitchDocked=ignore
```

### What this does:

- Prevents the system from sleeping when the lid is closed
- Ensures the system stays active when running on external power
- Prevents sleep behavior when docked or used in a server-like state

---

After saving the changes, I applied them by restarting the service:

```bash
sudo systemctl restart systemd-logind
```

---

## Result

At this point, the laptop is fully configured as a lightweight home server:

- SSH is enabled and accessible over the local network
- Firewall rules allow secure remote access
- Sleep and lid-close behavior is disabled
- The system can run headless (lid closed) without interruption

This setup allows me to place the machine in a fixed location and manage it entirely remotely from my main desktop.

---

## Next Steps

- Configure static IP for consistent access
- Set up key-based SSH authentication (disable password login)
- Explore Docker and Portainer for service deployment
- Begin hosting basic services (file server, security tools, etc.)
