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

## Lab - Listing the pods
```
oc get pods
oc get pod
oc get po
```

Expected output
<pre>
</pre>

## Scale up deployment to 6 pod instances
```
oc scale deploy/nginx --replicas=6
```

Expected output
<pre>
(jegan@tektutor.org)$ oc scale deploy/nginx --replicas=6
deployment.apps/nginx scaled
(jegan@tektutor.org)$ oc get po -w
NAME                     READY   STATUS              RESTARTS   AGE
nginx-6d666844f6-6cs5r   1/1     Running             0          40m
nginx-6d666844f6-7wwn4   1/1     Running             0          40m
nginx-6d666844f6-jhh57   0/1     ContainerCreating   0          2s
nginx-6d666844f6-lx8fs   0/1     ContainerCreating   0          2s
nginx-6d666844f6-nrnp4   0/1     ContainerCreating   0          2s
nginx-6d666844f6-prdw8   1/1     Running             0          40m
nginx-6d666844f6-jhh57   1/1     Running             0          8s
nginx-6d666844f6-nrnp4   1/1     Running             0          19s
nginx-6d666844f6-lx8fs   1/1     Running             0          24s
^C(jegan@tektutor.org)$ oc get po 
NAME                     READY   STATUS    RESTARTS   AGE
nginx-6d666844f6-6cs5r   1/1     Running   0          41m
nginx-6d666844f6-7wwn4   1/1     Running   0          41m
nginx-6d666844f6-jhh57   1/1     Running   0          29s
nginx-6d666844f6-lx8fs   1/1     Running   0          29s
nginx-6d666844f6-nrnp4   1/1     Running   0          29s
nginx-6d666844f6-prdw8   1/1     Running   0          41m
</pre>

## Scale down deployment to 1 pod instance
```
oc scale deploy nginx --replicas=1
```

Expected output
<pre>
(jegan@tektutor.org)$ oc scale deploy/nginx --replicas=1
deployment.apps/nginx scaled
(jegan@tektutor.org)$ oc get po -w
NAME                     READY   STATUS    RESTARTS   AGE
nginx-6d666844f6-prdw8   1/1     Running   0          43m
</pre>


## Deleting a deployment
```
oc delete deploy/nginx
```

Expected output
<pre>
(jegan@tektutor.org)$ oc delete deploy/nginx
deployment.apps "nginx" deleted
(jegan@tektutor.org)$ oc get deploy,rs,po
No resources found in default namespace.
</pre>


## ELK vs EFK

ELK Stack
- Elastic Search ( Time series database that store, retrieves and can be query for application logs )
- LogStash - converts the logs in a format that Elastic Search database can store
- Kibana

EFK Stack
- Elastic Search ( Time series database )
- Fluentd - - converts the logs in a format that Elastic Search database can store
- Kibana - Web Dashboard GUI which allows you to query and view logs from a centralized web page conveniently

Both stacks are used for viewing application logs from web dashboard.

Assume, that your microservice container is the main container in the Pod which creates application logs.
As so many Pods of this microservice might be running within the OpenShift cluster, it is not easy to check logs from a centralized location.

That's there either ELK, EFK or Splunk is used.

In such cases, the Pod will have a side-car(supporting) container along side the main application. The side-car container's main purpose is to collect the logs produced by your main container and push to a centralized location which then will be retrieved and shown in the Kibana Dashboard GUI.
