# Day 9

## Lab - CI/CD with Tekton (Build app binary from source code, build container image, push image to docker hub and deploy application into OpenShift )

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
