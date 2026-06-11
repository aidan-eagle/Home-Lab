# Pi-hole Deployment and DNS Filtering Homelab Project

## Overview

After completing the setup and security hardening of my Linux Mint homelab server, I wanted to explore network-wide ad and tracker blocking without immediately replacing my Verizon DNS infrastructure. My goal was to learn how DNS filtering works, how containerized applications are deployed, and how network traffic can be monitored and controlled using self-hosted services.

Due to Verizon's DNS behavior, attempts to permanently redirect all network DNS traffic to Pi-hole would often be overridden by the router. Rather than fighting the ISP configuration, I chose to deploy Pi-hole in a controlled testing environment and route only my desktop computer through it. This allowed me to safely test functionality, monitor DNS traffic, and gain hands-on experience with DNS filtering technologies.

---

# Learning Docker

Before deploying Pi-hole, I needed a way to run applications without directly installing them on the operating system.

## What is Docker?

For non-technical users, Docker can be thought of as a lightweight shipping container for software.

Instead of installing an application directly onto a computer, Docker packages everything the application needs into a self-contained environment called a **container**.

Benefits include:

- Applications remain isolated from the operating system
- Easy deployment and removal
- Consistent behavior across systems
- Simplified upgrades and maintenance
- Reduced dependency conflicts

This approach is commonly used in enterprise environments because it allows administrators to deploy services quickly without affecting the underlying server.

---

# Installing Portainer

While Docker can be managed entirely from the command line, I wanted a more user-friendly way to manage containers.

## What is Portainer?

Portainer is a web-based management interface for Docker.

For non-technical users, Portainer functions like a control panel for containerized applications.

Instead of memorizing Docker commands, Portainer allows administrators to:

- View running containers
- Deploy applications
- Monitor resource usage
- Manage storage volumes
- Configure networking
- Restart or update services

This makes managing a homelab significantly easier and provides a centralized dashboard for future services.

---

# Preparing the Server for Pi-hole

## Understanding Port 53

Before deploying Pi-hole, I had to verify that DNS port 53 was available.

DNS (Domain Name System) is the service responsible for translating website names such as:

```text
google.com
youtube.com
github.com
```

into IP addresses that computers can use.

Pi-hole acts as a DNS server, which means it must listen on port 53.

Initially, Linux Mint was already using port 53 through the system resolver service. Because only one service can control a port at a time, Pi-hole could not start until the port was freed.

After identifying the conflict, I reconfigured the system so that Pi-hole could take ownership of port 53 and function as the primary DNS service.

### Verification Command

```bash
sudo ss -tulpn | grep :53
```

---

# Deploying Pi-hole

Using Portainer, I deployed Pi-hole through a Docker Compose stack.

The deployment automatically:

- Created the container
- Configured networking
- Created persistent storage
- Exposed the required DNS and web management ports

Pi-hole was configured with:

```yaml
services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest

    ports:
      - "53:53/tcp"
      - "53:53/udp"
      - "8081:80/tcp"

    environment:
      TZ: "America/New_York"
      FTLCONF_webserver_api_password: "REPLACE_WITH_STRONG_PASSWORD"
      FTLCONF_dns_listeningMode: "ALL"

    volumes:
      - pihole_data:/etc/pihole
      - dnsmasq_data:/etc/dnsmasq.d

    restart: unless-stopped

volumes:
  pihole_data:
  dnsmasq_data:
```

Once deployed, Pi-hole became accessible through its web administration dashboard.

---

# Configuring DNS Filtering

After deployment, I configured Pi-hole to begin filtering unwanted traffic.

## Blocklists

Blocklists are collections of known advertising, tracking, telemetry, and malicious domains.

When a device attempts to access a blocked domain, Pi-hole intercepts the request and prevents the connection.

Benefits include:

- Reduced advertisements
- Increased privacy
- Lower bandwidth usage
- Faster browsing experience
- Protection against known malicious domains

I researched community-recommended blocklists and imported them into Pi-hole to improve filtering effectiveness.

---

# Configuring Upstream DNS

Pi-hole still needs another DNS provider to resolve legitimate domains that are not blocked.

This provider is called an upstream DNS server.

## Why I Chose Quad9

I configured Pi-hole to use Quad9 as its upstream DNS provider.

For non-technical users, Quad9 is similar to Google's DNS or Cloudflare's DNS, but with additional security features.

Benefits include:

- Privacy-focused DNS resolution
- Threat intelligence integration
- Blocking of known malicious domains
- Reduced DNS tracking compared to many ISP DNS providers

This ensures that DNS requests leaving my network are routed through a trusted provider while still benefiting from Pi-hole filtering.

---

# Testing Pi-hole

Before modifying my entire network, I tested Pi-hole using only my desktop workstation.

This approach minimized risk and allowed me to validate functionality before making network-wide changes.

## Windows DNS Configuration

### Open Network Settings

```text
Settings
→ Network & Internet
→ Wi-Fi
→ Hardware Properties
→ DNS Server Assignment
→ Edit
```

### Configure DNS

Set:

```text
Preferred DNS:
<PIHOLE_SERVER_IP>
```

Example:

```text
192.168.1.50
```

Leave Alternate DNS blank during testing.

Save the configuration.

---

# Verification

After configuring DNS, I verified connectivity using:

```powershell
nslookup google.com
```

Successful responses confirmed that the desktop was using Pi-hole as its DNS resolver.

Within the Pi-hole dashboard, I observed:

- DNS query counts increasing
- Client activity appearing
- Domains being blocked by configured blocklists

This confirmed that DNS traffic was successfully flowing through the Pi-hole server.

---

# Commands Used

## Docker Installation

```bash
sudo apt update
sudo apt install docker.io -y
sudo systemctl enable --now docker
```

Verify Docker:

```bash
docker ps
```

---

## Portainer Deployment

```bash
docker volume create portainer_data
```

```bash
docker run -d \
--name portainer \
--restart=unless-stopped \
-p 9443:9443 \
-v /var/run/docker.sock:/var/run/docker.sock \
-v portainer_data:/data \
portainer/portainer-ce:latest
```

---

## Port 53 Verification

```bash
sudo ss -tulpn | grep :53
```

---

## Pi-hole Container Verification

```bash
docker ps
```

```bash
docker logs pihole
```

---

## DNS Testing

```powershell
nslookup google.com
```

```powershell
ipconfig /all
```

---

# Skills Learned

## Linux Administration

- Service management
- Port management
- Docker deployment
- Container troubleshooting

## Networking

- DNS fundamentals
- Port usage and conflicts
- Local network configuration
- DNS traffic analysis

## Cybersecurity

- DNS filtering
- Ad and tracker blocking
- Threat intelligence through Quad9
- Privacy-focused network configuration

## Containerization

- Docker container deployment
- Volume management
- Service isolation
- Container networking

## Troubleshooting

- Identifying port conflicts
- Resolving DNS configuration issues
- Validating network connectivity
- Testing and monitoring service functionality

---

# Outcome

Successfully deployed Pi-hole within a Docker container managed through Portainer and validated DNS filtering functionality on a desktop workstation. This project provided hands-on experience with containerized services, DNS infrastructure, network monitoring, and privacy-focused security controls while building a foundation for future homelab services and cybersecurity projects.
