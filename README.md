# Ansible Managed Raspberry Pi Cluster Hosting K8's (Kids Next Door themed!)


# My Setup
- 4 Raspberry Pi 4 Model B 4GB running 64-bit Ubuntu 20.04 (worker nodes)
- 1 Raspberry Pi 4 Model B 2GB running 64-bit Ubuntu 20.04 (head node)
- 1 2-TB HDD plugged into head node
- 5 port network switch
# Setup
- May need to give your vscode/terminal/whatever certain file access on Mac

## Flash and connect
For each Pi:
1. Flash 64-but Ubuntu Server 20.04 onto SD card
2. `ansible-playbook playbooks/setup-flashed-sd-card.yml -i inventory --limit HOST --ask-vault-pass ` where host is defined in your inventory file
3. Put SD card into your Pi and boot
4. `ssh ubuntu@static-ip` and change the password on login and disconnect
5. `ssh-copy-id -i ~/.ssh/id_ed25519.pub ubuntu@static-ip` (now you can run playbooks against that host)

## Update
`ansible-playbook playbooks/update/enable.yml -i inventory`
## Mount HDD and Ensure Access
### On the head node once the HDD has been plugged in:

```sh
sudo mkfs.ext4 /dev/sda1
sudo mkdir /clusterdata
sudo chown nobody.nogroup -R /clusterdata
sudo chmod 777 -R /clusterdata
```

Need the UUID of this drive....

`blkid | grep sda1` will give us that `UUID="8ff90304-1038-4d72-a211-fe5257feb00d"`
Now need to edit `/etc/fstab` and add `UUID=8ff90304-1038-4d72-a211-fe5257feb00d /clusterdata ext4 defaults 0 2`
Now `sudo mount -a`

Loose permissions...

```sh
sudo chown nobody.nogroup -R /clusterdata
sudo chmod -R 766 /clusterdata
```

Install NFS Server

`sudo apt install nfs-kernel-server -y`

Add `/clusterdata    10.0.0.0/24(rw,sync,no_root_squash,no_subtree_check)` to `/etc/exports`

And finally run `sudo exportfs -a`

### Now for each worker node:
Install `sudo apt install nfs-common -y`

Create + Mount:
```sh
sudo mkdir /clusterdata
sudo chown nobody.nogroup /clusterdata
sudo chmod -R 777 /clusterdata
```

add `10.0.0.200:/clusterdata    /clusterdata    nfs    defaults   0 0` to `/etc/fstab`

Finally `sudo mount -a`

## Install Docker