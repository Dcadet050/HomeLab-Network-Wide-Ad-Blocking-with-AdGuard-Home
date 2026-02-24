# HomeLab: Network Wide Ad Blocking with AdGuard Home - Commands Used

## 1. Ubuntu Server Setup
Update and upgrade packages:
sudo apt update && sudo apt upgrade -y

## 2. SSH Access Configuration
Enable SSH service to start on boot:
sudo systemctl enable ssh
sudo systemctl start ssh

Connect from macOS to Ubuntu VM:
ssh username@192.168.1.192

## 3. AdGuard Home Installation
Download and install AdGuard Home:
curl -s -S -L https://raw.githubusercontent.com/AdguardTeam/AdGuardHome/master/scripts/install.sh | sh

Verify installation and start service:
./AdGuardHome -s status
./AdGuardHome -s install

## 4. DNS Port Conflict Resolution
Disable systemd-resolved (to free port 53):
sudo systemctl disable systemd-resolved
sudo systemctl stop systemd-resolved

Edit resolv.conf to add alternate DNS:
sudo nano /etc/resolv.conf

Add the following lines:
nameserver 1.1.1.1
nameserver 8.8.8.8

Restart AdGuard Home service:
./AdGuardHome -s restart

## 5. Static IP Configuration (Netplan)
Edit Netplan configuration:
sudo nano /etc/netplan/00-installer-config.yaml

Example configuration:
network:
  version: 2
  ethernets:
    enp0s3:
      dhcp4: no
      addresses: [192.168.1.192/24]
      gateway4: 192.168.1.1
      nameservers:
        addresses: [1.1.1.1, 8.8.8.8]

Apply Netplan changes:
sudo netplan apply

## 6. Router Configuration (Verizon)
Access router settings: http://192.168.1.1  
Set Primary DNS to Ubuntu VM static IP: 192.168.1.192  
Optional: Set Secondary DNS to 1.1.1.1 or 8.8.8.8

## 7. Verification & Testing
Test network connectivity:
ping example.com

Check AdGuard Home status:
./AdGuardHome -s status
