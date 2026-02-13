# Proxmox
How to manager your LVM disk on Proxmox



Process

Welcome to this step-by-step guide on how to delete the `local-lvm` storage and reallocate its space to the `local` storage in Proxmox VE.

Make sure to back up any important data before proceeding, as these operations can result in data loss.

First, let's remove the `local-lvm` storage configuration. This command will remove the configuration from Proxmox VE without deleting any data.

`pvesm remove local-lvm`

Next, use the `lvremove` command to remove the logical volume associated with `local-lvm`. Note that this will delete all data on this volume.

`lvremove pve/data`

Now, we will extend the root logical volume. First, check if the volume group `pve` has free space.

`vgdisplay pve`

If free space is available, extend the root logical volume.

`lvextend -l +100%FREE /dev/pve/root`

Resize the file system to use the extended logical volume. The command depends on your file system type. For ext4, use:

`resize2fs /dev/pve/root`

If you are using XFS, the command is:

`xfs_growfs /`

Next, modify the `/etc/pve/storage.cfg` file to update the `local` storage configuration. Ensure it allows for all desired content types.

`nano /etc/pve/storage.cfg`

Here’s an example of what the content should look like:

`dir: local path /var/lib/vz content images,iso,vztmpl,backup,rootdir,snippets maxfiles 0`

To apply the changes, restart the necessary Proxmox VE services.

`systemctl restart pvedaemon`

`systemctl restart pveproxy`

`systemctl restart pvestatd`

Finally, verify that the `local-lvm` storage has been removed and that the `local` storage is utilizing all available disk space.

`pvesm status`

****************************************************************************************************************************************
How to fixed on client ssh if you have issues key to communicate with both side.

`nano ~/.ssh/config`
# Put this one inside on your file ssh
`#Legacy Changes
    KexAlgorithms +diffie-hellman-group1-sha1
    HostKeyAlgorithms +ssh-rsa
    Ciphers +aes256-cbc,aes128-cbc,3des-cbc`
