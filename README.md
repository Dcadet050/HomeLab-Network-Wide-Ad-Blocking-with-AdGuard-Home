# HomeLab-Network-Wide-Ad-Blocking-with-AdGuard-Home
In this project, I used Ubuntu Server in VirtualBox VM on macOS, to run ADGuard Home. By doing this, I'm able to filter DNS requests for all devices on my home network. 

### Environment Setup

- Installed Virtualbox on macOS Sonoma 14.8.3
- I created a new VM with:
- Name: Ubuntu-AdGuard
- OS Type: Linux / Ubuntu (64-bit)
- 2-4 GB RAM / 2 CPU cores
- Virtual Hard Disk: 25GB
- Ubuntu ISO server attached to VM and configured boot order 

<img width="945" height="545" alt="Image" src="https://github.com/user-attachments/assets/f13c4e88-2a90-48ac-954f-007d4f286775" />

<img width="435" height="357" alt="Image" src="https://github.com/user-attachments/assets/630756bd-4982-4096-a050-63084308c1e9" />

### Network Configuration:

Next, I configured a network adapter in VirtualBox.

- Mode: Bridged Adapter / VM gets its own LAN IP visible to all devices.

<img width="703" height="339" alt="Image" src="https://github.com/user-attachments/assets/0e3d8c61-96af-4879-843a-fb1021930fb0" />

<img width="663" height="411" alt="Image" src="https://github.com/user-attachments/assets/a69fe040-9141-4573-b390-dbb2db3de6f1" />

After this step, a static IP was set on Ubuntu Server Using Netplan:

<img width="390" height="204" alt="Image" src="https://github.com/user-attachments/assets/145dbb81-da85-4d07-89b0-9e171af2ec03" />

### System Preparation:

The Ubuntu server was updated using sudo apt update && sudo apt upgrade -y. I made sure to verify system packages and kernel were up to date. 

<img width="632" height="419" alt="Image" src="https://github.com/user-attachments/assets/388bb661-14cd-4735-a0ca-17e5efd1df77" />

<img width="656" height="421" alt="Image" src="https://github.com/user-attachments/assets/32fd90ab-6a34-448b-b61e-6bfc8a39f649" />

### SSH Access Configuration: 

I enabled SSH to start on boot. This is the code that was used:

sudo systemctl enable ssh
sudo systemctl start ssh

Connected from MacOS:

ssh username@192.168.1.192

This is the expected output: 

_The authenticity of host '192.168.1.192' can't be established.
ECDSA key fingerprint is SHA256:XXXXXXXXXXXXXX.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Welcome to Ubuntu 22.04 LTS (GNU/Linux 5.15.0-106-generic x86_64)
username@Ubuntu-AdGuard:~$### Installing AdGuard Home:_ 

Explanation: 

SSH from macOS to the Ubuntu VM ensures network access and remote management. 

### AdGuard Home Installation:

AdGuard home with installed and downloaded with the official script:

curl -s -S -L https://raw.githubusercontent.com/AdguardTeam/AdGuardHome/master/scripts/install.sh | sh.

<img width="659" height="430" alt="Image" src="https://github.com/user-attachments/assets/8d0f5263-a139-476f-99c9-7b1b7792ea9b" />

I've also verified installation with ./AdGuardHome -s status and installed AdGuard service to start on boot using ./AdGuardHome -s install.


### DNS Port Conflict Resolution: 

In this section, their was a port 53 binding issue that was by systemd-resolved. To solve this issue I used:
sudo systemctl disable systemd-resolved
sudo systemctl stop systemd-resolved
sudo nano /etc/resolv.conf
# Added:
nameserver 1.1.1.1
nameserver 8.8.8.8

<img width="660" height="415" alt="Image" src="https://github.com/user-attachments/assets/2a4cd065-7dca-4572-a946-6420f58ec965" />

<img width="662" height="417" alt="Image" src="https://github.com/user-attachments/assets/30d9317c-b6ef-4a9e-9f76-33ca1cb8a1e6" />

<img width="655" height="413" alt="Image" src="https://github.com/user-attachments/assets/dbcc5242-2289-4f4f-bfed-4aa2a778d098" />

In order for this to work, restarting AdGuard home is necessary. 
Code used:
./AdGuardHome -s restart

### Router / Network Configuration:
My household uses Verizon. I accessed Verizon's router admin interface using http://192.168.1.1). Enabling manual DNS configuration allowed me to set my Primary DNS to 192.168.1.192 (AdGuard VM) in order for it to connect. 

<img width="1402" height="697" alt="Image" src="https://github.com/user-attachments/assets/20fe4146-f769-4ce7-888d-1cb6c4733db7" />

<img width="1421" height="690" alt="Image" src="https://github.com/user-attachments/assets/de13973c-0fbd-4028-b9af-0d53744a8b36" />

### Testing & Verification:

I created a username and password for AdGuard Home. Next, I logged in to verify my configurations were working. Checking the statistics dashboard was important to make sure network wide ad blocking was filtering DNS requests. 

<img width="1415" height="705" alt="Image" src="https://github.com/user-attachments/assets/49c7b0a5-5af5-4c34-8b63-c8b0340d508d" />

<img width="1417" height="697" alt="Image" src="https://github.com/user-attachments/assets/003d69bc-6f05-4195-be45-07f55ce7d0ea" />

<img width="1412" height="699" alt="Image" src="https://github.com/user-attachments/assets/e4cad811-748d-4e94-8dd0-0beb5e1d2dba" />

These screenshots show that I confirmed ads and trackers blocked on multiple devices and monitored DNS queries and client statistics in real time. 

### Conclusion:
- Built a network-level ad-blocking solution using open-source tools
- Managed Linux servers, networking, and DNS in a virtualized environment
- Installed and configured AdGuard Home on Ubuntu Server
- Configured static IPs, bridged networking, and router DNS
- Tested and monitored network-wide ad blocking across multiple clients
- Gained hands on experience with:
- VirtualBox
- Ubuntu Server CLI
- systemd services
- Netplan networking
- Router DNS configuration
