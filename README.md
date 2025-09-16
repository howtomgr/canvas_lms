# canvas-lms Installation Guide

canvas-lms is a free and open-source learning management. Canvas provides open source learning management system

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
  - RAM: 8GB minimum
  - Storage: 50GB for data
  - Network: HTTP/HTTPS
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 80 (default canvas-lms port)
  - Various services
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

# Install canvas-lms
sudo dnf install -y canvas_lms

# Enable and start service
sudo systemctl enable --now canvas-lms

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
canvas-lms --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install canvas-lms
sudo apt install -y canvas_lms

# Enable and start service
sudo systemctl enable --now canvas-lms

# Configure firewall
sudo ufw allow 80

# Verify installation
canvas-lms --version
```

### Arch Linux

```bash
# Install canvas-lms
sudo pacman -S canvas_lms

# Enable and start service
sudo systemctl enable --now canvas-lms

# Verify installation
canvas-lms --version
```

### Alpine Linux

```bash
# Install canvas-lms
apk add --no-cache canvas_lms

# Enable and start service
rc-update add canvas-lms default
rc-service canvas-lms start

# Verify installation
canvas-lms --version
```

### openSUSE/SLES

```bash
# Install canvas-lms
sudo zypper install -y canvas_lms

# Enable and start service
sudo systemctl enable --now canvas-lms

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
canvas-lms --version
```

### macOS

```bash
# Using Homebrew
brew install canvas_lms

# Start service
brew services start canvas_lms

# Verify installation
canvas-lms --version
```

### FreeBSD

```bash
# Using pkg
pkg install canvas_lms

# Enable in rc.conf
echo 'canvas-lms_enable="YES"' >> /etc/rc.conf

# Start service
service canvas-lms start

# Verify installation
canvas-lms --version
```

### Windows

```bash
# Using Chocolatey
choco install canvas_lms

# Or using Scoop
scoop install canvas_lms

# Verify installation
canvas-lms --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/canvas_lms

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
canvas-lms --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable canvas-lms

# Start service
sudo systemctl start canvas-lms

# Stop service
sudo systemctl stop canvas-lms

# Restart service
sudo systemctl restart canvas-lms

# Check status
sudo systemctl status canvas-lms

# View logs
sudo journalctl -u canvas-lms -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add canvas-lms default

# Start service
rc-service canvas-lms start

# Stop service
rc-service canvas-lms stop

# Restart service
rc-service canvas-lms restart

# Check status
rc-service canvas-lms status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'canvas-lms_enable="YES"' >> /etc/rc.conf

# Start service
service canvas-lms start

# Stop service
service canvas-lms stop

# Restart service
service canvas-lms restart

# Check status
service canvas-lms status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start canvas_lms
brew services stop canvas_lms
brew services restart canvas_lms

# Check status
brew services list | grep canvas_lms
```

### Windows Service Manager

```powershell
# Start service
net start canvas-lms

# Stop service
net stop canvas-lms

# Using PowerShell
Start-Service canvas-lms
Stop-Service canvas-lms
Restart-Service canvas-lms

# Check status
Get-Service canvas-lms
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream canvas_lms_backend {
    server 127.0.0.1:80;
}

server {
    listen 80;
    server_name canvas_lms.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name canvas_lms.example.com;

    ssl_certificate /etc/ssl/certs/canvas_lms.example.com.crt;
    ssl_certificate_key /etc/ssl/private/canvas_lms.example.com.key;

    location / {
        proxy_pass http://canvas_lms_backend;
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
    ServerName canvas_lms.example.com
    Redirect permanent / https://canvas_lms.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName canvas_lms.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/canvas_lms.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/canvas_lms.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:80/
    ProxyPassReverse / http://127.0.0.1:80/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend canvas_lms_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/canvas_lms.pem
    redirect scheme https if !{ ssl_fc }
    default_backend canvas_lms_backend

backend canvas_lms_backend
    balance roundrobin
    server canvas_lms1 127.0.0.1:80 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R canvas_lms:canvas_lms /etc/canvas_lms
sudo chmod 750 /etc/canvas_lms

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
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
sudo systemctl status canvas-lms

# View logs
sudo journalctl -u canvas-lms -f

# Monitor resource usage
top -p $(pgrep canvas_lms)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/canvas_lms"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/canvas_lms-backup-$DATE.tar.gz" /etc/canvas_lms /var/lib/canvas_lms

echo "Backup completed: $BACKUP_DIR/canvas_lms-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop canvas-lms

# Restore from backup
tar -xzf /backup/canvas_lms/canvas_lms-backup-*.tar.gz -C /

# Start service
sudo systemctl start canvas-lms
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u canvas-lms -n 100
sudo tail -f /var/log/canvas_lms/canvas_lms.log

# Check configuration
canvas-lms --version

# Check permissions
ls -la /etc/canvas_lms
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 80

# Test connectivity
telnet localhost 80

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep canvas_lms)

# Check disk I/O
iotop -p $(pgrep canvas_lms)

# Check connections
ss -an | grep 80
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  canvas_lms:
    image: canvas_lms:latest
    ports:
      - "80:80"
    volumes:
      - ./config:/etc/canvas_lms
      - ./data:/var/lib/canvas_lms
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update canvas_lms

# Debian/Ubuntu
sudo apt update && sudo apt upgrade canvas_lms

# Arch Linux
sudo pacman -Syu canvas_lms

# Alpine Linux
apk update && apk upgrade canvas_lms

# openSUSE
sudo zypper update canvas_lms

# FreeBSD
pkg update && pkg upgrade canvas_lms

# Always backup before updates
tar -czf /backup/canvas_lms-pre-update-$(date +%Y%m%d).tar.gz /etc/canvas_lms

# Restart after updates
sudo systemctl restart canvas-lms
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/canvas_lms

# Clean old logs
find /var/log/canvas_lms -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/canvas_lms
```

## Additional Resources

- Official Documentation: https://docs.canvas_lms.org/
- GitHub Repository: https://github.com/canvas_lms/canvas_lms
- Community Forum: https://forum.canvas_lms.org/
- Best Practices Guide: https://docs.canvas_lms.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
