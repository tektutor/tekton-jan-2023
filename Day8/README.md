# Day 8



## ⛹️‍♂️ Lab - Creating a Tekton Task that will clone the source code from GitHub into a Persistent Storage (NFS)

You need to install the git-clone task from Tekton Hub
```
tkn hub install task git-clone
```

Now you may refer the installed task in the below taskrun
```
cd ~/tekton-jan-2023
git pull

cd Day8/tekton
oc apply -f tekton-persistent-storage.yml
oc create -f github-clone-taskrun.yml
```

To check the output
```
tkn tr list
tkn tr logs -f --last
```

## ⛹️‍♀️ Lab - Build java project that uses maven as a build tool in a TaskRun picking the source code from persistent storage where the Previous Lab exercise cloned the source code

You need to install the git-clone task from Tekton Hub
```
tkn hub install task maven
```

Now you may refer the installed task in the below taskrun

```
cd ~/tekton-jan-2023
git pull

cd Day8/tekton
oc create -f maven-java-task.yml
```

To check the output
```
tkn tr list
tkn tr logs -f --last
```

Troubleshooting, permission denied error
```
su -
chmod 777 -R /var/nfs_shares
oc create -f maven-java-task.yml
tkn tr logs -f --last
```
