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

## Case Study 1 - Using Red Hat OpenShift Service Mesh
You may refer the step by step instructions in detail here https://redhat-scholars.github.io/istio-tutorial/istio-tutorial/1.9.x/index.html

Installing Red Hat OpenShift Service Mesh, the below Operators has to be installed in OpenShift via Webconsole
```
1. OpenShift Elasticsearch Operator
2. Red Hat OpenShift Distributed Tracing Platform (Jaeger)
3. Kiali Operator
4. Red Hat OpenShift Service Mesh
```

Demo
```
cd ~
git clone https://github.com/redhat-developer-demos/istio-tutorial istio-tutorial
cd istio-tutorial istio-tutorial
```

Create a project 'istio-system'

From the OpenShift Installed Operators Menu, in the Provided API, click on Istio Service Mesh Control Plane.
Navigate to Proxy > Networking > Traffic Control > Outbound > Policy and type 'REGISTRY_ONLY' and click on Create. Create ServiceMeshMemberRoll and type the below code
```
apiVersion: maistra.io/v1
kind: ServiceMeshMemberRoll
metadata:
  name: default
  namespace: istio-system
spec:
  members:
    - tutorial
```

Check the pods
```
kubectl get pods -w -n istio-system
```

### Deploying Microservices

Create the customer microservice
```
kubectl apply -f customer/kubernetes/Deployment.yml -n tutorial
kubectl create -f customer/kubernetes/Service.yml -n tutorial
kubectl get pods -w -n tutorial
```

Deploy the GateWay and VirtualService
```
kubectl create -f customer/kubernetes/Gateway.yml -n tutorial
kubectl get all -l app=istio-ingressgateway -n istio-system
```

Validate Ingress
```
export GATEWAY_URL=$(kubectl get route istio-ingressgateway -n istio-system -o=jsonpath="{.spec.host}")

curl $GATEWAY_URL/customer

kubectl logs \
$(kubectl get pods -n tutorial |grep customer|awk '{ print $1 }'|head -1) \
-c customer -n tutorial
```

Deploy the Preference Service
```
kubectl apply -f preference/kubernetes/Deployment.yml -n tutorial
kubectl create -f preference/kubernetes/Service.yml -n tutorial
kubectl get pods -w -n tutorial
curl $GATEWAY_URL/customer
kubectl logs \
$(kubectl get pods -n tutorial |grep preference|awk '{ print $1 }'|head -1) \
-c preference -n tutorial
```

Deploy the Recommendation Service
```
kubectl apply -f recommendation/kubernetes/Deployment.yml -n tutorial
kubectl create -f recommendation/kubernetes/Service.yml -n tutorial
kubectl get pods -w -n tutorial
curl $GATEWAY_URL/customer
kubectl logs \
$(kubectl get pods -n tutorial |grep recommendation|awk '{ print $1 }'|head -1) \
-c recommendation -n tutorial
```
## Case Study 3 - Using ArgoCD for Continuous Delivery



