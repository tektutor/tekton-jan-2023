# Day 10

## ⛹️‍♂️ Lab - Triggering Tekton Pipeline using polling

We need to install a tekton-polling operator.  This helps triggering pipeline on local openshift setup.

In case of local OpenShift setup, GitHub won't be able to invoke the OpenShift public route, hence the only way to trigger pipeline is using the polling operator.

Let's install the polling operator
```
oc apply -f https://github.com/bigkevmcd/tekton-polling-operator/releases/download/v0.4.0/release-v0.4.0.yaml
```

Let's create the pipeline and setup the github polling
```
cd ~/openshift-sep-2022-batch2
git pull

cd Day10/poll-and-trigger-tekon-pipeline
oc apply -f java-cicd-pipeline.yml
oc apply -f github-trigger.yml
```

## ⛹️‍♂️ Lab - TekTon Trigger
```
cd ~/openshift-sep-2022-batch2
git pull
cd Day10/github-webhook-trigger-using-curl-tekton-pipeline

oc apply -f tekton-trigger.yaml
```

Check if the event listener pod is running
```
 oc get pod --field-selector=status.phase==Running
```

Find the service name
```
oc get el tektutor-trigger-listener -o=jsonpath='{.status.configuration.generatedName}'
```

Expected output
<pre>
(jegan@tektutor.org)$ <b>oc get el tektutor-trigger-listener -o=jsonpath='{.status.configuration.generatedName}'</b>
el-tektutor-trigger-listener
</pre>

List the service now
```
oc get service $(oc get el tektutor-trigger-listener -o=jsonpath='{.status.configuration.generatedName}')
```

Expected output
<pre>
(jegan@tektutor.org)$ <b>oc get service $(oc get el tektutor-trigger-listener -o=jsonpath='{.status.configuration.generatedName}')</b>
NAME                           TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)             AGE
el-tektutor-trigger-listener   ClusterIP   172.30.214.111   <none>        8080/TCP,9000/TCP   3m58s
</pre>

Load the service name into an environment variable
```
SVC_NAME=$(oc get el tektutor-trigger-listener -o=jsonpath='{.status.configuration.generatedName}')
```

Expected output
<pre>
(jegan@tektutor.org)$ <b>SVC_NAME=$(oc get el tektutor-trigger-listener -o=jsonpath='{.status.configuration.generatedName}')</b>
</pre>

Let's create a route now
```
oc create route edge ${SVC_NAME}-route --service=${SVC_NAME}
```

Expected output
<pre>
(jegan@tektutor.org)$ <b>oc create route edge ${SVC_NAME}-route --service=${SVC_NAME}</b>
route.route.openshift.io/el-tektutor-trigger-listener-route created
</pre>

See if the route is created
```
oc get route ${SVC_NAME}-route
```

Expected output
<pre>
(jegan@tektutor.org)$ <b>oc get route ${SVC_NAME}-route</b>
NAME                                 HOST/PORT                                                        PATH   SERVICES                       PORT            TERMINATION   WILDCARD
el-tektutor-trigger-listener-route   el-tektutor-trigger-listener-route-jegan.apps.ocp.tektutor.org          el-tektutor-trigger-listener   http-listener   edge          None
</pre>

Let's us invoke the Trigger now
```
HOOK_URL=https://$(oc get route ${SVC_NAME}-route -o=jsonpath='{.spec.host}')
curl --insecure --location --request POST ${HOOK_URL} --header 'Content-Type: application/json' --data-raw '{"name":"run-my-app","run-it":"yes-please"}'
```

Expected output
<pre>
(jegan@tektutor.org)$ <b>HOOK_URL=https://$(oc get route ${SVC_NAME}-route -o=jsonpath='{.spec.host}')</b>
(jegan@tektutor.org)$ <b>curl --insecure --location --request POST ${HOOK_URL} --header 'Content-Type: application/json' --data '{"name":"run-my-app","run-it":"yes-please"}'</b>
{"eventListener":"tektutor-trigger-listener","namespace":"jegan","eventListenerUID":"778e9325-11b9-488a-81ef-e8b05866cc16","eventID":"ed729332-efb5-4e6b-95ef-565480db0f58"}
</pre>

## OpenShift Case Study
- Using Red Hat OpenShift Service Mesh
- Using Gloo Mesh in Multi-cluster OpenShift 
- Using ArgoCD ( Not in agenda, based on request it is added )
