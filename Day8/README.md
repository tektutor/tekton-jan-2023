# Day 8

## Lab - Creating a Tekton Task that will clone the source code from GitHub into a Persistent Storage (NFS)
```
cd ~/tekton-jan-2023
git pull

cd Day8/tekton
oc apply -f tekton-persistent-storage.yml
oc create -f github-clone-taskrun.yml
```

## Lab - Build java project that uses maven as a build tool in a TaskRun that picks the source from persistent storage where the Previous Lab cloned the source code

```
cd ~/tekton-jan-2023
git pull

cd Day8/tekton
oc create -f maven-java-task.yml
```
