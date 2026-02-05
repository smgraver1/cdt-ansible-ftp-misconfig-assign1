# Vulnerable FTP Infrastructure - CDT - Stephen Graver - Assignment #2

## Project Overview
This project automates the deployment of a multi-node vulnerable infrastructure. It utilizes Ansible to programmatically misconfigure a DMZ FTP Gateway and establish Internal Pivot Targets for our competition. It also serves as a controlled environment where reconnaisssance practice and access via service misconfigurations can be used. As well as lateral movement between different segmented networks.

## Vulnerability Description
The primary vulnerability is a anaymous FTP misconfiguration combined with a sensitive credential data leak. The vsftpd service is configured to allow unauthorized access to a world writable drop zone directory. This contains a hidden file with cleartext credentials for internal systems and the app and db ip. This shows the high risk of insecure FTP and the danger of credential reuse and not a secure password either.

## Prerequists
* Target OS: Ubuntu 22.04 LTS (3 VMs)
* Main OS: Ubuntu 22.04 LTS (1 VM) Running Ansible and VS code server
* Ansible Version: 2.9+
* Required Python Packages: python3
* Other: SSH connectivity between the control node and target vms.
* Main machine with Vs code running with Remote-SSH and Ansible extension installed.

## Quick Start
* Run these commands in order from your project root on VS code remote-ssh onto main OS VM.
  - ./setup-ssh.sh # Test and setup connectivity between all 4 VMs
  - ansible all -m ping # Very ansible can communicate with all VMs
  - ansible-playbook playbook.yml -i inventory.ini # Deploy the stack
  - ansible-playbook validate.yml -i inventory.ini # Audit the security state and vsftpd

## Documentation
[Deployment Guide](./docs/DEPLOYMENT.md) - Step by step setup, prerequistes and verification screenshots
[Exploitation Guide](./docs/EXPLOITATION.md) - Walkthrough of the attack vector, reconnaissance, and defensive mitigations

## Competition Use Cases
* Red Team: Use the environment to practice automated harvesting of leaked credentials and pivoting from a gateway to internal server assets.
* Blue Team: Analyze vsftpd logs to detect anonymous logins and develop Ansible playbooks to remediate misconfigurations across all 3 VMs.
* Grey Team: Rapidly deploy consistent "reset-ready" vulernable labs for competitiors using a few commands.

## Technical Details
* Ansible playbook utilizies a role based structure to perform:
  - Installs the vsftpd package and manages the service state
  - Deploys a custom teomplate for vsftpd.conf that enables anonymous access and bypasses chroot security checks for testing
  - Establishes a directory with specific permissions to satisfy vsftpd security while leaving a public folder writable to anyone
  - Creates a leaked hidden file called .dev_notes.txt containing internal db and app IP addresses and credentials for pivot targets

## Troubleshooting
* 500 OOPS Error when login to FTP - This is due to a writable root in chroot, ensures /var/ftp is 0755 for permissions and owned by root
* Undefined Variable - Inventory name mismatch, make sure all group_vars/all.yml match inventory.ini
* Connection Failed - SSH/ProxyJump config, verify the Jump Host and credentials in inventory.ini are correct

## Repository Structure

ansible-vuln-deployment/
├── README.md        # Main documentation
├── playbook.yml     # Main Ansible playbook
├── inventory.ini    # inventory
├── vars/
│   └── main.yml     # Variables
├── templates/       # Jinja2 templates (if needed)
│   └── config.j2
├── files/            # Static files to copy
│   └── vulnerable_app.php
├── screenshots/      # All screenshots
│   ├── deployment/
│   │   ├── ansible_run.png
│   │   ├── service_running.png
│   │   └── web_interface.png
│   └── exploitation/
│       ├── recon_scan.png
│       ├── exploit_step1.png
│       └── compromised_system.png
├── docs/
│   ├── DEPLOYMENT.md   # Deployment documentation
│   └── EXPLOITATION.md # Exploitation guide
└── .gitignore          # Git ignore file