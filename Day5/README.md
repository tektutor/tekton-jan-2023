# Day 5

## Installing NFS Server for External Persistent Storage
```
sudo apt update
sudo apt install -y nfs-kernel-server
sudo mkdir -p /var/nfs_shared_folders/mysql
sudo chown -R nobody:nogroup /var/nfs_shared_folders/
sudo chmod -R 777 /var/nfs_shared_folders
sudo vim /etc/exports
sudo exportfs -a
sudo systemctl restart nfs-kernel-server
sudo ufw allow from 10.0.2.15/24 to any port nfs
sudo ufw enable
sudo ufw status
sudo apt install nfs-common
```
<pre>
rw - gives the client read and write access to the volume
sync - forces NFS to write changes to disk before replying. 
no_subtree_check - prevents subtree checking, avoids problems when a file is renamed while the client has it opened
no_root_squash - By default, NFS translates requests from a root user remotely into a non-privileged user on the server. This was intended as security feature to prevent a root account on the client from using the file system of the host as root. no_root_squash disables this behavior for certain shares
</pre>

#### Create an file /etc/exports as root user and add the below using vim editor
```
/var/nfs_shared_folders/mysql      192.168.122.0/24(rw,sync,no_subtree_check)
```

