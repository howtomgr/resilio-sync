# resilio-sync Installation Guide

resilio-sync is a free and open-source P2P file synchronization. Formerly BitTorrent Sync, provides decentralized file synchronization

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Supported Operating Systems](#supported-operating-systems)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Service Management](#service-management)
6. [Troubleshooting](#troubleshooting)
7. [Security Considerations](#security-considerations)
8. [Performance Tuning](#performance-tuning)
9. [Backup and Restore](#backup-and-restore)
10. [System Requirements](#system-requirements)
11. [Support](#support)
12. [Contributing](#contributing)
13. [License](#license)
14. [Acknowledgments](#acknowledgments)
15. [Version History](#version-history)
16. [Appendices](#appendices)

## 1. Prerequisites

- **Hardware Requirements**:
  - CPU: 1 core minimum
  - RAM: 512MB minimum
  - Storage: 1GB for app
  - Network: P2P networking
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 8888 (default resilio-sync port)
  - Sync on various ports
- **Dependencies**:
  - See official documentation for specific requirements
- **System Access**: root or sudo privileges required


## 2. Supported Operating Systems

This guide supports installation on:
- RHEL 8/9 and derivatives (CentOS Stream, Rocky Linux, AlmaLinux)
- Debian 11/12
- Ubuntu 20.04/22.04/24.04 LTS
- Arch Linux (rolling release)
- Alpine Linux 3.18+
- openSUSE Leap 15.5+ / Tumbleweed
- SUSE Linux Enterprise Server (SLES) 15+
- macOS 12+ (Monterey and later) 
- FreeBSD 13+
- Windows 10/11/Server 2019+ (where applicable)

## 3. Installation

### RHEL/CentOS/Rocky Linux/AlmaLinux

```bash
# Install EPEL repository if needed
sudo dnf install -y epel-release

# Install resilio-sync
sudo dnf install -y resilio-sync

# Enable and start service
sudo systemctl enable --now resilio-sync

# Configure firewall
sudo firewall-cmd --permanent --add-port=8888/tcp
sudo firewall-cmd --reload

# Verify installation
resilio-sync --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install resilio-sync
sudo apt install -y resilio-sync

# Enable and start service
sudo systemctl enable --now resilio-sync

# Configure firewall
sudo ufw allow 8888

# Verify installation
resilio-sync --version
```

### Arch Linux

```bash
# Install resilio-sync
sudo pacman -S resilio-sync

# Enable and start service
sudo systemctl enable --now resilio-sync

# Verify installation
resilio-sync --version
```

### Alpine Linux

```bash
# Install resilio-sync
apk add --no-cache resilio-sync

# Enable and start service
rc-update add resilio-sync default
rc-service resilio-sync start

# Verify installation
resilio-sync --version
```

### openSUSE/SLES

```bash
# Install resilio-sync
sudo zypper install -y resilio-sync

# Enable and start service
sudo systemctl enable --now resilio-sync

# Configure firewall
sudo firewall-cmd --permanent --add-port=8888/tcp
sudo firewall-cmd --reload

# Verify installation
resilio-sync --version
```

### macOS

```bash
# Using Homebrew
brew install resilio-sync

# Start service
brew services start resilio-sync

# Verify installation
resilio-sync --version
```

### FreeBSD

```bash
# Using pkg
pkg install resilio-sync

# Enable in rc.conf
echo 'resilio-sync_enable="YES"' >> /etc/rc.conf

# Start service
service resilio-sync start

# Verify installation
resilio-sync --version
```

### Windows

```bash
# Using Chocolatey
choco install resilio-sync

# Or using Scoop
scoop install resilio-sync

# Verify installation
resilio-sync --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/resilio-sync

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
resilio-sync --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable resilio-sync

# Start service
sudo systemctl start resilio-sync

# Stop service
sudo systemctl stop resilio-sync

# Restart service
sudo systemctl restart resilio-sync

# Check status
sudo systemctl status resilio-sync

# View logs
sudo journalctl -u resilio-sync -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add resilio-sync default

# Start service
rc-service resilio-sync start

# Stop service
rc-service resilio-sync stop

# Restart service
rc-service resilio-sync restart

# Check status
rc-service resilio-sync status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'resilio-sync_enable="YES"' >> /etc/rc.conf

# Start service
service resilio-sync start

# Stop service
service resilio-sync stop

# Restart service
service resilio-sync restart

# Check status
service resilio-sync status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start resilio-sync
brew services stop resilio-sync
brew services restart resilio-sync

# Check status
brew services list | grep resilio-sync
```

### Windows Service Manager

```powershell
# Start service
net start resilio-sync

# Stop service
net stop resilio-sync

# Using PowerShell
Start-Service resilio-sync
Stop-Service resilio-sync
Restart-Service resilio-sync

# Check status
Get-Service resilio-sync
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream resilio-sync_backend {
    server 127.0.0.1:8888;
}

server {
    listen 80;
    server_name resilio-sync.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name resilio-sync.example.com;

    ssl_certificate /etc/ssl/certs/resilio-sync.example.com.crt;
    ssl_certificate_key /etc/ssl/private/resilio-sync.example.com.key;

    location / {
        proxy_pass http://resilio-sync_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName resilio-sync.example.com
    Redirect permanent / https://resilio-sync.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName resilio-sync.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/resilio-sync.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/resilio-sync.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:8888/
    ProxyPassReverse / http://127.0.0.1:8888/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend resilio-sync_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/resilio-sync.pem
    redirect scheme https if !{ ssl_fc }
    default_backend resilio-sync_backend

backend resilio-sync_backend
    balance roundrobin
    server resilio-sync1 127.0.0.1:8888 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R resilio-sync:resilio-sync /etc/resilio-sync
sudo chmod 750 /etc/resilio-sync

# Configure firewall
sudo firewall-cmd --permanent --add-port=8888/tcp
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on
```

## Database Setup

See official documentation for database configuration requirements.

## Performance Optimization

### System Tuning

```bash
# Basic system tuning
echo 'net.core.somaxconn = 65535' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_max_syn_backlog = 65535' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## Monitoring

### Basic Monitoring

```bash
# Check service status
sudo systemctl status resilio-sync

# View logs
sudo journalctl -u resilio-sync -f

# Monitor resource usage
top -p $(pgrep resilio-sync)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/resilio-sync"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/resilio-sync-backup-$DATE.tar.gz" /etc/resilio-sync /var/lib/resilio-sync

echo "Backup completed: $BACKUP_DIR/resilio-sync-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop resilio-sync

# Restore from backup
tar -xzf /backup/resilio-sync/resilio-sync-backup-*.tar.gz -C /

# Start service
sudo systemctl start resilio-sync
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u resilio-sync -n 100
sudo tail -f /var/log/resilio-sync/resilio-sync.log

# Check configuration
resilio-sync --version

# Check permissions
ls -la /etc/resilio-sync
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 8888

# Test connectivity
telnet localhost 8888

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep resilio-sync)

# Check disk I/O
iotop -p $(pgrep resilio-sync)

# Check connections
ss -an | grep 8888
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  resilio-sync:
    image: resilio-sync:latest
    ports:
      - "8888:8888"
    volumes:
      - ./config:/etc/resilio-sync
      - ./data:/var/lib/resilio-sync
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update resilio-sync

# Debian/Ubuntu
sudo apt update && sudo apt upgrade resilio-sync

# Arch Linux
sudo pacman -Syu resilio-sync

# Alpine Linux
apk update && apk upgrade resilio-sync

# openSUSE
sudo zypper update resilio-sync

# FreeBSD
pkg update && pkg upgrade resilio-sync

# Always backup before updates
tar -czf /backup/resilio-sync-pre-update-$(date +%Y%m%d).tar.gz /etc/resilio-sync

# Restart after updates
sudo systemctl restart resilio-sync
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/resilio-sync

# Clean old logs
find /var/log/resilio-sync -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/resilio-sync
```

## Additional Resources

- Official Documentation: https://docs.resilio-sync.org/
- GitHub Repository: https://github.com/resilio-sync/resilio-sync
- Community Forum: https://forum.resilio-sync.org/
- Best Practices Guide: https://docs.resilio-sync.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
