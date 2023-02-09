# Day 9

## ⛹️‍♂️ Lab - Create your first Tekton pipeline
```
cd ~/tekton-jan-2023
git pull

cd Day9/tekton
oc project

oc apply -f first-pipeline.yml
tkn pipeline list
tkn pipeline start first-pipeline
tkn pipelinerun list
tkn pipelinerun logs -f --last
```

## ⛹️‍♂️ Lab - Create your second Tekton pipeline
```
cd ~/tekton-jan-2023
git pull

cd Day9/tekton
oc project

oc apply -f second-pipeline.yml
tkn pipeline list
tkn pipeline start second-pipeline
tkn pipelinerun list
tkn pipelinerun logs -f --last
```


## ⛹️‍♂️ Lab - CI/CD with Tekton (Build app binary from source code, build container image, push image to docker hub and deploy application into OpenShift )

Buildah utility requires root permission inside the Pod that runs on OpenShift, hence we need to create a service account with necessary permissions
```
cd ~/tekton-jan-2023
git pull

cd Day9/tekton/build-and-push
oc project

oc create -f mybuild-security-ctx.yml
oc create serviceaccount fsgroup-runasany
oc adm policy add-scc-to-user my-scc -z fsgroup-runasany
oc adm policy add-scc-to-user privileged -z fsgroup-runasany
```

Create a secret capturing Docker Hub Login Credentials
```
export USERNAME=<your-dockerhub-username>
export PASSWORD=<your-dockerhub-password>
oc create secret generic dockerhub-credentials --from-literal username=$USERNAME --from-literal password=$PASSWORD
```

Now let's deploy the 
```
cd ~/tekton-jan-2023
git pull

cd Day9/tekton/build-and-push
oc project

oc apply -f build-and-push-task.yml
oc apply -f deploy-task.yml
oc apply -f pipeline.yml
oc create -f pipelinerun.yml
tkn pr logs -f --last
```

### Troubleshooting
In case, you are getting forbidden issues while deploying then try this
```
oc create role deployment --verb=create,delete,get,list,patch,update,watch --resource=deployment
oc create role service --verb=create,delete,get,list,patch,update,watch --resource=service
oc create role route --verb=create,delete,get,list,patch,update,watch --resource=route
oc adm policy add-role-to-user deployment fsgroup-runasany -n jegan
oc adm policy add-role-to-user service fsgroup-runasany
oc adm policy add-role-to-user route fsgroup-runasany
```


## Lab - Let's run another Java pipeline
```
https://medium.com/tektutor/openshift-ci-cd-with-tekton-faa88ba45656
```
