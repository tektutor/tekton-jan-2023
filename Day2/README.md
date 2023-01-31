# Day 2

## Deploying an application into OpenShift
```
oc create deployment nginx --image=nginx:latest
```

Expected output
<pre>
(jegan@tektutor.org)$ <b>oc create deployment nginx --image=nginx:latest</b>
Warning: would violate PodSecurity "restricted:latest": allowPrivilegeEscalation != false (container "nginx" must set securityContext.allowPrivilegeEscalation=false), unrestricted capabilities (container "nginx" must set securityContext.capabilities.drop=["ALL"]), runAsNonRoot != true (pod or container "nginx" must set securityContext.runAsNonRoot=true), seccompProfile (pod or container "nginx" must set securityContext.seccompProfile.type to "RuntimeDefault" or "Localhost")
deployment.apps/nginx created
</pre>


## Listing the deployments
```
oc get deployments
oc get deployment
oc get deployment
```

Expected output
<pre>
deployment.apps/nginx created
(jegan@tektutor.org)$ oc get deployments
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   1/1     1            1           4m52s
(jegan@tektutor.org)$ oc get deployment
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   1/1     1            1           4m54s
(jegan@tektutor.org)$ oc get deploy
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   1/1     1            1           4m56s
</pre>


## Lab - Listing the replicasets
```
oc get replicasets
oc get replicaset
oc get rs
```

Expected output
<pre>
(jegan@tektutor.org)$ oc get replicasets
NAME               DESIRED   CURRENT   READY   AGE
nginx-6d666844f6   1         1         1       5m31s
(jegan@tektutor.org)$ oc get replicaset
NAME               DESIRED   CURRENT   READY   AGE
nginx-6d666844f6   1         1         1       5m36s
(jegan@tektutor.org)$ oc get rs
NAME               DESIRED   CURRENT   READY   AGE
nginx-6d666844f6   1         1         1       5m38s
</pre>
