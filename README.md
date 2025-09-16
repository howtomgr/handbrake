# handbrake Installation Guide

handbrake is a free and open-source video transcoder. HandBrake provides open source video transcoder

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
  - CPU: 4+ cores
  - RAM: 4GB minimum
  - Storage: 20GB for temp
  - Network: GUI/CLI
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port N/A (default handbrake port)
  - None
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

# Install handbrake
sudo dnf install -y handbrake

# Enable and start service
sudo systemctl enable --now handbrake

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
sudo firewall-cmd --reload

# Verify installation
handbrake --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install handbrake
sudo apt install -y handbrake

# Enable and start service
sudo systemctl enable --now handbrake

# Configure firewall
sudo ufw allow N/A

# Verify installation
handbrake --version
```

### Arch Linux

```bash
# Install handbrake
sudo pacman -S handbrake

# Enable and start service
sudo systemctl enable --now handbrake

# Verify installation
handbrake --version
```

### Alpine Linux

```bash
# Install handbrake
apk add --no-cache handbrake

# Enable and start service
rc-update add handbrake default
rc-service handbrake start

# Verify installation
handbrake --version
```

### openSUSE/SLES

```bash
# Install handbrake
sudo zypper install -y handbrake

# Enable and start service
sudo systemctl enable --now handbrake

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
sudo firewall-cmd --reload

# Verify installation
handbrake --version
```

### macOS

```bash
# Using Homebrew
brew install handbrake

# Start service
brew services start handbrake

# Verify installation
handbrake --version
```

### FreeBSD

```bash
# Using pkg
pkg install handbrake

# Enable in rc.conf
echo 'handbrake_enable="YES"' >> /etc/rc.conf

# Start service
service handbrake start

# Verify installation
handbrake --version
```

### Windows

```bash
# Using Chocolatey
choco install handbrake

# Or using Scoop
scoop install handbrake

# Verify installation
handbrake --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/handbrake

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
handbrake --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable handbrake

# Start service
sudo systemctl start handbrake

# Stop service
sudo systemctl stop handbrake

# Restart service
sudo systemctl restart handbrake

# Check status
sudo systemctl status handbrake

# View logs
sudo journalctl -u handbrake -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add handbrake default

# Start service
rc-service handbrake start

# Stop service
rc-service handbrake stop

# Restart service
rc-service handbrake restart

# Check status
rc-service handbrake status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'handbrake_enable="YES"' >> /etc/rc.conf

# Start service
service handbrake start

# Stop service
service handbrake stop

# Restart service
service handbrake restart

# Check status
service handbrake status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start handbrake
brew services stop handbrake
brew services restart handbrake

# Check status
brew services list | grep handbrake
```

### Windows Service Manager

```powershell
# Start service
net start handbrake

# Stop service
net stop handbrake

# Using PowerShell
Start-Service handbrake
Stop-Service handbrake
Restart-Service handbrake

# Check status
Get-Service handbrake
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream handbrake_backend {
    server 127.0.0.1:N/A;
}

server {
    listen 80;
    server_name handbrake.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name handbrake.example.com;

    ssl_certificate /etc/ssl/certs/handbrake.example.com.crt;
    ssl_certificate_key /etc/ssl/private/handbrake.example.com.key;

    location / {
        proxy_pass http://handbrake_backend;
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
    ServerName handbrake.example.com
    Redirect permanent / https://handbrake.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName handbrake.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/handbrake.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/handbrake.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:N/A/
    ProxyPassReverse / http://127.0.0.1:N/A/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend handbrake_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/handbrake.pem
    redirect scheme https if !{ ssl_fc }
    default_backend handbrake_backend

backend handbrake_backend
    balance roundrobin
    server handbrake1 127.0.0.1:N/A check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R handbrake:handbrake /etc/handbrake
sudo chmod 750 /etc/handbrake

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
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
sudo systemctl status handbrake

# View logs
sudo journalctl -u handbrake -f

# Monitor resource usage
top -p $(pgrep handbrake)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/handbrake"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/handbrake-backup-$DATE.tar.gz" /etc/handbrake /var/lib/handbrake

echo "Backup completed: $BACKUP_DIR/handbrake-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop handbrake

# Restore from backup
tar -xzf /backup/handbrake/handbrake-backup-*.tar.gz -C /

# Start service
sudo systemctl start handbrake
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u handbrake -n 100
sudo tail -f /var/log/handbrake/handbrake.log

# Check configuration
handbrake --version

# Check permissions
ls -la /etc/handbrake
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep N/A

# Test connectivity
telnet localhost N/A

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep handbrake)

# Check disk I/O
iotop -p $(pgrep handbrake)

# Check connections
ss -an | grep N/A
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  handbrake:
    image: handbrake:latest
    ports:
      - "N/A:N/A"
    volumes:
      - ./config:/etc/handbrake
      - ./data:/var/lib/handbrake
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update handbrake

# Debian/Ubuntu
sudo apt update && sudo apt upgrade handbrake

# Arch Linux
sudo pacman -Syu handbrake

# Alpine Linux
apk update && apk upgrade handbrake

# openSUSE
sudo zypper update handbrake

# FreeBSD
pkg update && pkg upgrade handbrake

# Always backup before updates
tar -czf /backup/handbrake-pre-update-$(date +%Y%m%d).tar.gz /etc/handbrake

# Restart after updates
sudo systemctl restart handbrake
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/handbrake

# Clean old logs
find /var/log/handbrake -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/handbrake
```

## Additional Resources

- Official Documentation: https://docs.handbrake.org/
- GitHub Repository: https://github.com/handbrake/handbrake
- Community Forum: https://forum.handbrake.org/
- Best Practices Guide: https://docs.handbrake.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
