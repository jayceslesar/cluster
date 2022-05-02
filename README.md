# Ansible Managed Raspberry Pi Cluster Hosting K8's (Kids Next Door themed!)


# My Setup
4 Raspberry Pi 4 Model B 4GB running 64-bit Ubuntu 20.04 (worker nodes)
1 Raspberry Pi 4 Model B 2GB running 64-bit Ubuntu 20.04 (head node)
1 2-TB HDD plugged into head node
5 port network switch
# Setup
- May need to give your vscode/terminal/whatever certain file access on Mac

## Flash and connect
For each Pi:
1. Flash 64-but Ubuntu Server 20.04 onto SD card
2. 2 run `ansible-playbook playbooks/setup-flashed-sd-card.yml -i inventory --limit HOST --ask-vault-pass ` where host is defined in your inventory file
3. Put SD card into your Pi and boot
4. `ssh ubuntu@static-ip` and change the password on login and disconnect
5. `ssh-copy-id -i ~/.ssh/id_ed25519.pub ubuntu@static-ip` (now you can run playbooks against that host)

## Update
`ansible-playbook playbooks/update/enable.yml -i inventory`
## Mount HDD and Ensure Access

## Install Docker