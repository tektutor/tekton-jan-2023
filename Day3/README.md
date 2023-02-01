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
- represents a group of Load Balanced Pods from same deployment

## Creating an internal ClusterIP Service
```
oc expose deploy/nginx --type=ClusterIP --port=8080
oc expose deploy/nginx --port=8080

oc get services
oc describe svc/nginx

oc get endpoints
```

Expected output
<pre>
(jegan@tektutor.org)$ oc expose deploy nginx --type=ClusterIP --port=8080
service/nginx exposed

(jegan@tektutor.org)$ oc get services
NAME    TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
nginx   ClusterIP   172.30.168.20   <none>        8080/TCP   7s

(jegan@tektutor.org)$ oc describe svc/nginx
Name:              nginx
Namespace:         jegan
Labels:            app=nginx
Annotations:       <none>
Selector:          app=nginx
Type:              ClusterIP
IP Family Policy:  SingleStack
IP Families:       IPv4
IP:                172.30.168.20
IPs:               172.30.168.20
Port:              <unset>  8080/TCP
TargetPort:        8080/TCP
Endpoints:         10.128.0.99:8080,10.128.2.22:8080,10.131.0.129:8080
Session Affinity:  None
Events:            <none>

(jegan@tektutor.org)$ oc get endpoints
NAME    ENDPOINTS                                             AGE
nginx   10.128.0.99:8080,10.128.2.22:8080,10.131.0.129:8080   104s
</pre>

## Lab - Delete the clusterIP Service
```
oc delete svc/nginx
```

Expected output
<pre>
(jegan@tektutor.org)$ oc delete svc/nginx
service "nginx" deleted
</pre>
