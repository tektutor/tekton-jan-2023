# Day 3

## Lab - Finding the currently active project
```
oc project
```

Expected output
<pre>
(root@tektutor.org)# oc project
No project has been set. Pass a project name to make that the default.
</pre>


## Lab - Switching to a particular project
```
oc project jegan
oc project
```
Expected output
<pre>
(root@tektutor.org)# oc project jegan
Now using project "jegan" on server "https://api.ocp.tektutor.org:6443".

(root@tektutor.org)# oc project
Using project "jegan" on server "https://api.ocp.tektutor.org:6443".
</pre>

## What is a Service in Kubernetes/OpenShift?
- is a way to expose your applications 
- is of two types
  1. Internal Service ( ClusterIP - accessible only within the OpenShift cluster )
  2. External Service ( NodePort and LoadBlanacer Service accessible outside the cluster )
- represents a group of Load Balanced Pods
