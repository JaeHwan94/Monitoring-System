# Install NFS Server
>   ```bash
>   $ sudo apt update
>
>   $ sudo apt install nfs-kernel-server
>
>   $ mkdir -p /mnt/nfs # Create NFS Folder
>
>   $ sudo chown nobody:nogroup /mnt/nfs
>   $ sudo chomod 777 /mnt/nfs
>
>   $ vim /etc/exports
>
>   /mnt/nfs ClientIP(rw,no_subtree_check), ClientIP2(rw,no_subtree_check) ....
>
>   $ sudo exportfs -a
>   $ sudo systemctl restart nfs-kernel-server
>
>   $ sudo ufw allo 192.168.0.0/24 # Open firewall
>   ```
