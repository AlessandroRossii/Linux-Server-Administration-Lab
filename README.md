# Linux Server Administration Lab



**Project Description**: A personal hands-on laboratory project focused on learning System Administration (SysAdmin) fundamentals, server hardening, and web service management.

Environment: Ubuntu Server 24.04.3 (Virtualised via VMware).

#### Goal: To build, configure, and secure a functional web server from scratch.



### Ongoing progress:



1. ##### Initial Setup \& System Updates (**Completed**)

* Successful installation of Ubuntu Server on a Virtual Machine.
* Network configuration and remote access setup via SSH.
* Package management: system updates and upgrades performed using apt update \&\& upgrade.

!\[Initial startup; eventual download and upgrades](Screenshots/Setup\_Upgrade.PNG)



2. ##### Web Server Deployment (Nginx) (**Completed**)



* Installed and activated the Nginx web service.
* Service status verification using systemctl.
* Real-time troubleshooting and traffic analysis via Access Logs (/var/log/nginx/access.log).



3. ##### User Management \& File Permissions (**Completed**)



* Advanced File System navigation (cd, ls -la, absolute vs. relative paths).
* File and directory operations (touch, mkdir, mv).
* System Security: Implemented chmod to manage Read/Write/Execute permissions (e.g., 600 for private files).
* Process management and resource monitoring using htop.

!\[monitoring resources using htop:](Screenshots/htop.PNG)



4. ##### Security \& Hardening (Firewall) (**Completed**)



* Installation and configuration of UFW (Uncomplicated Firewall).
* Access Rule Configuration:
* Port 22 (SSH) -> Allowed (to prevent lockout).
* Port 80/443 (Web) -> Allowed.
* Default Policy -> Deny Incoming.

!\[checking status of the UFW (uncomplicated Firewall):](Screenshots/status\_ufw.PNG)



5. ##### Advanced Authentication (SSH Keys) (**Done**)



Generating Public/Private key pairs.

Disabling password-based login to prevent Brute Force attacks. Had a bit of troubles for this one



Goal: Transition from password-based login to Asymmetric Key Encryption using the Ed25519 algorithm.



###### 5.1 Implementation Steps



* Key Generation: Created a public/private key pair on the local host (Windows) using ssh-keygen -t ed25519.
* Key Deployment: Transferred the public key to the server's authorized\_keys file using **ssh-copy-id**
* Service Hardening: Modified /etc/ssh/sshd\_config to disable vulnerable access methods

 	PasswordAuthentication no

 	PubkeyAuthentication yes

 	PermitRootLogin no



Why i chose some of these things:



###### chose Ed25519 over RSA Standard

it provides higher security with smaller key sizes and faster performance and its newer too.





###### Why the use of **ssh-copy-id**

This command automates three steps:

Creates the ~/.ssh directory on the server (if it doesn't already exist).

Sets the correct permissions (700 for the directory, 600 for the authorized\_keys file).

Appends your public key to the file without overwriting any existing keys.

All that means that it pretty much removes the human factor of error if it was made by hand



###### Disabling PasswordAuthentication



When the server will be connected to the internet it will probably receive thousands of login attempts from bots, so using a key means the server won't even listen to a password request; The bot has nothing to guess



Why not leave it active as a backup? If you leave it enabled, you render the use of keys useless. grandpa used to say the chain is only as strong as its weakest link.



###### Disabling PermitRootLogin:

this one is pretty much just to make an hacke life a bit harded. the root user exist in every server; forcing access through a standard user, a hacker would have to guess both the username and the key.

you won't log in direcly as root just because of a matter of Auditing. if multiple people manage a server, you want to know who did what





###### 5.2 Problems and Lessons Learned the hard way:



for some reason SSH client (Windows) could not find the private key file in the local .ssh folder, so i tried to verify the path with ls $HOME\\.ssh\\ and ensured the correct naming.



!\[Private key missing from the directory:](Screenshots/problem.PNG)



I then found out that i generated keys inside the server LoL instead of the local host, so i re-generated the key on Windows (Host) and pushed to Server (Guest) i then deleted redundant keys on the server.



!\[Keys inside the host:](Screenshots/fixed\_host.PNG)



Incorrect permissions on .ssh or authorized\_keys prevented key reading. Enforced chmod 700 for the directory and 600 for the file.





#### 6\. Future Steps:

* probably **Fail2Ban** Integration to automatically ban IP addresses that show malicious signs like too many failed connection attempts.
* maybe add **HTTPS** cause as of now the server use http
