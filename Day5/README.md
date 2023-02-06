# Day 5

## Installing NFS Server for External Persistent Storage in Ubuntu
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

## Installing NFS Server in CentOS 7.x
```
su -
yum install -y nfs-utils
systemctl start nfs-server.service
systemctl enable nfs-server.service
systemctl status nfs-server.service

mkdir  -p /var/nfs_shares/user1
mkdir  -p /var/nfs_shares/user2
mkdir  -p /var/nfs_shares/user3
mkdir  -p /var/nfs_shares/user4
mkdir  -p /var/nfs_shares/user5
mkdir  -p /var/nfs_shares/user6
mkdir  -p /var/nfs_shares/user7

chown -R nobody:nobody /var/nfs_shares
chmod -R 777 /var/nfs_shares

ls -l /var/nfs_shares/
```

Create a file vim /etc/exports with the below content
<pre>
/var/nfs_shares/user1  192.168.122.0/24(rw,sync,no_subtree_check)
/var/nfs_shares/user2  192.168.122.0/24(rw,sync,no_subtree_check)
/var/nfs_shares/user3  192.168.122.0/24(rw,sync,no_subtree_check)
/var/nfs_shares/user4  192.168.122.0/24(rw,sync,no_subtree_check)
/var/nfs_shares/user5  192.168.122.0/24(rw,sync,no_subtree_check)
/var/nfs_shares/user6  192.168.122.0/24(rw,sync,no_subtree_check)
/var/nfs_shares/user6  192.168.122.0/24(rw,sync,no_subtree_check)
</pre>

Restart the nfs server
```
su -

systemctl restart nfs-server
exportfs -arv
exportfs  -s
firewall-cmd --permanent --add-service=nfs
firewall-cmd --permanent --add-service=rpc-bind
firewall-cmd --permanent --add-service=mountd
firewall-cmd --reload 
showmount -e localhost
```

## Lab - Deployming mysql db server that uses an external Persistent Volume

### What is Persistent Volume (PV)?
- OpenShift Administrators can manually create Disk Volumes of different size and types
- In case, your OpenShift cluster has Storage Class created by OpenShift Administrators then the Persistent Volume will be created automatically by the OpenShift and then it lets your application use the Persistent Volume (PV)

### What is Persistent Volume Claim (PVC)?
- this is how your application can request for external storage in Kubernetes/OpenShift
- should mention
  - What type of storage it requires
  - How much disk space ?
  - Optionally, can also mention, what is the storage class ( NFS, AWS S3, EBS, etc., )
  - Optionally, Persistent Volume that has some specific Labels

You may try the lab exercise now ...
```
cd ~/tekton-jan-2023
git pull

cd Day5/persistent-storage-and-claims/

oc create -f mysql-pvc.yml --save-config
oc create -f mysql-pv.yml --save-config
oc create -f mysql-deploy.yml --save-config
```


