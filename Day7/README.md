# Day 7

## Lab - Creating a custom Nginx Operator 

### Initialize the ansible operator project
```
su -
mkdir -p /root/jegan
cd /root/jegan
operator-sdk init --plugins=ansible --domain=tektutor.org
```

Expected output is
<pre>
(jegan@tektutor.org)$ operator-sdk init --plugins=ansible --domain=tektutor.org
Writing kustomize manifests for you to edit...
Next: define a resource with:
$ operator-sdk create api
</pre>

## Creating an API
```
cd /root/jegan
operator-sdk create api --group training --version v1 --kind Nginx --generate-role 
```

## Let's update the Ansible role

Currently the /roles/nginx/tasks/main.yml file will be empty
<pre>
(jegan@tektutor.org)$ <b>cat roles/nginx/tasks/main.yml</b>
</pre>
Edit the /roles/nginx/tasks/main.yml and add the below coe
<pre>
---
- name: start nginx
  kubernetes.core.k8s:
    definition:
      kind: Deployment
      apiVersion: apps/v1
      metadata:
        name: '{{ ansible_operator_meta.name }}-nginx'
        namespace: '{{ ansible_operator_meta.namespace }}'
      spec:
        replicas: "{{size}}"
        selector:
          matchLabels:
            app: nginx
        template:
          metadata:
            labels:
              app: nginx
          spec:
            containers:
            - name: nginx
              image: bitnami/nginx:latest
              ports:
                - containerPort: 8080
</pre>

## Update the default variable size to 3

Append the below line in roles/nginx/defaults/main.yml

```
size: 1
```

## You need to login to your redhat account to authorize downloading redhat container images
```
docker login registry.redhat.io
```

<pre>
(jegan@tektutor.org)$ <b>docker login registry.redhat.io</b>
Authenticating with existing credentials...
Login did not succeed, error: Error response from daemon: Get https://registry.redhat.io/v2/: unauthorized: Please login to the Red Hat Registry using your Customer Portal credentials. Further instructions can be found here: https://access.redhat.com/articles/3399531
Username (youremail@gmail.com): <type-your-redhat-login>
Password: <type-your-redhat-password>
WARNING! Your password will be stored unencrypted in /home/jegan/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
</pre>

## Build the OpenShift operator image
```
make docker-build IMG=tektutor/nginx-openshift-operator:1.0 
```

## Troubleshooting version conflicts
You may get this kind of version conflict errors while building your operator image.

You need to edit the /home/jegan/projects/nginx-operator/requirements.yml and update the version to 0.4.0 as necessary.

Once you update the requirements.yml with the tool version installed on your system, you can expect the build go thru.

## Build your operator image
```
make docker-build IMG=tektutor/nginx-openshift-operator:1.0
```

## Login to your Docker Hub account
```
docker login
```

Expected output is
<pre>
(jegan@tektutor.org)$ <b>docker login</b>
Authenticating with existing credentials...
WARNING! Your password will be stored unencrypted in /home/jegan/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
</pre>

## Push your OpenShift operator image to your Docker Hub repository
In my case I already created a tektutor/nginx-openshift-operator public repository in my Docker Hub account.

```
docker push tektutor/nginx-openshift-operator:1.0
```

## Deploying our nginx-operator into the OpenShift cluster
```
make deploy IMG=tektutor/nginx-openshift-operator:1.0
```

## Check your deployment in the cluster
```
oc get deploy -n nginx-operator-system
```

## Lab - Now you can create our Nginx Custom Resource as shown below

Now you may create the nginx resource as shown below
```
su -
mkdir -p /root/jegan
cd /root/jegan

oc project jegan
```

Now, let's create an nginx.yml file with below content

<pre>
Create an yaml file named nginx.yml with below content
apiVersion: training.tektutor.org/v1
kind: Nginx
metadata:
  name: nginx-sample
spec:
  size: 5 
</pre>


You may now create the nginx resource as shown below
```
oc apply -f nginx.yml
```

You can issue the below command to observe that our custom nginx-controller automatically create an nginx deploy with 3 pod instances
```
oc get deploy -w
oc get rs,po
```

## What is Tekton?
- serverless CI/CD Platform
- you can created CI/CD Pipeline with declarative code written in YAML
- is a Kubernetes native CI/CD Framework, hence works in Kubernetes and OpenShift
- you may refer my Medium blog about Tekton here https://medium.com/tektutor/openshift-ci-cd-with-tekton-faa88ba45656

## What is a Tekton Pipeline?
- a collection of Tasks executed some in sequence and some in parallel
- each Pipeline has one to many Tasks

## What is Tekton PipelineRun?
- represents a single execution of a Pipeline
- each time a Pipeline is execute it creates an instance of PipelineRun
- PipelineRun provides the input parameters and configurations to Pipeline to run successfully

## What is a Tekton Task?
- each Task when executed creates a Pod
- each Task has one to many Steps
- each Step will create a container within the Task Pod

## What is Tekton TaskRun?
- each time a Task is executed, it creates an instance of TaskRun (ie. Pod)
- TaskRun provides values for the input parameters, and also provides configuration info for the Task to run properly

## What is Tekton Workspace?
- a directory
- the workspace directory will be used by Task to pick the input data and store the output data
- the Output produced by one Task can be an input to the next Task in the pipeline
- the directory could mount an emptyDir, PersistentVolume, ConfigMap, Secrets

## Custom Resource added by Tekton
```
oc get crds | grep tekton
```

Expected output
<pre>
jegan@tektutor.org)$ oc get crds|grep tekton
clustertasks.tekton.dev                                           2023-02-07T06:21:48Z
openshiftpipelinesascodes.operator.tekton.dev                     2023-02-07T06:20:54Z
pipelineresources.tekton.dev                                      2023-02-07T06:21:48Z
pipelineruns.tekton.dev                                           2023-02-07T06:21:48Z
pipelines.tekton.dev                                              2023-02-07T06:21:48Z
resolutionrequests.resolution.tekton.dev                          2023-02-07T06:21:48Z
runs.tekton.dev                                                   2023-02-07T06:21:48Z
taskruns.tekton.dev                                               2023-02-07T06:21:48Z
tasks.tekton.dev                                                  2023-02-07T06:21:48Z
tektonaddons.operator.tekton.dev                                  2023-02-07T06:20:51Z
tektonchains.operator.tekton.dev                                  2023-02-07T06:20:54Z
tektonconfigs.operator.tekton.dev                                 2023-02-07T06:20:51Z
tektonhubs.operator.tekton.dev                                    2023-02-07T06:20:54Z
tektoninstallersets.operator.tekton.dev                           2023-02-07T06:20:51Z
tektonpipelines.operator.tekton.dev                               2023-02-07T06:20:51Z
tektontriggers.operator.tekton.dev                                2023-02-07T06:20:52Z
</pre>


## Installing the Tekton Command Line Tool from OpenShift webconsole
```
1. Launch your OpenShift web console in Google chrome
2. Once you have logged in, click on the '?' on the top right corner and click on Command line tools
3. Download the tekton command line tool for linux x86_64
4. Extract the file with the command tar xvf tkn-linux-amd64.tar.gz
5. Move the tkn cli utility to /usr/local/bin as shown below
su -
mv /home/user1/Downloads/tkn /usr/local/bin
6. exit the root user login
```
In the above command, you need to modify the user as per your user name in the linux machine.


## Lab - Creating your first Tekton Task

A simple Tekton Task looks as below
<pre>
apiVersion: tekton.dev/v1beta1
kind: Task
metadata:
  name: hello
spec:
  steps:
  - name: echo
    image: ubuntu
    command:
    - echo
    args:
    - "Hello World !"
</pre>

You may now try creating a task as shown below
```
cd ~/tekton-jan-2023
git pull
cd Day7/tekton

oc apply -f hello-task.yml 
oc get tasks
tkn task list

```

Expected output
<pre>
(jegan@tektutor.org)$ oc apply -f hello-task.yml 
task.tekton.dev/hello created

(jegan@tektutor.org)$ oc get tasks
NAME    AGE
hello   6s

(jegan@tektutor.org)$ tkn taskrun logs -f hello-run-g9fcb
[echo] Hello World !
</pre>


## Lab - Tekton Task with multiple steps
```
cd ~/tekton-jan-2023
git pull
cd Day7/tekton

oc apply -f task-with-multiple-steps.yml
oc get tasks
tkn task list
tkn task start hello-task-with-multiple-steps
```

Expected output
<pre>
(jegan@tektutor.org)$ oc apply -f task-with-multiple-steps.yml 
task.tekton.dev/hello-task-with-multiple-steps created
(jegan@tektutor.org)$ oc get po
NAME                                  READY   STATUS      RESTARTS   AGE
hello-run-g9fcb-pod                   0/1     Completed   0          49m
nginx-sample-nginx-6986d4c448-4m7hj   1/1     Running     0          161m
nginx-sample-nginx-6986d4c448-5pzx5   1/1     Running     0          161m
nginx-sample-nginx-6986d4c448-89wbp   1/1     Running     0          163m
nginx-sample-nginx-6986d4c448-8wcvn   1/1     Running     0          161m
nginx-sample-nginx-6986d4c448-mxlcm   1/1     Running     0          161m
(jegan@tektutor.org)$ oc get tasks
NAME                             AGE
hello                            55m
hello-task-with-multiple-steps   93s
(jegan@tektutor.org)$ tkn task start hello-task-with-multiple-steps
TaskRun started: hello-task-with-multiple-steps-run-qjcl2

In order to track the TaskRun progress run:
tkn taskrun logs hello-task-with-multiple-steps-run-qjcl2 -f -n jegan
(jegan@tektutor.org)$ tkn taskrun logs hello-task-with-multiple-steps-run-qjcl2 -f -n jegan
[step-1] Step 1 => Hello World !

[step-2] Step 2 => Hello World !

[step-3] Step 3 => Hello World !
</pre>


## Lab - Tekton Task with multiple steps
```
cd ~/tekton-jan-2023
git pull
cd Day7/tekton

oc apply -f task-that-accepts-params.yml
oc get tasks
tkn task list
tkn task start hello-task-with-multiple-steps
```

Expected output
<pre>
n order to track the TaskRun progress run:
tkn taskrun logs hello-task-with-params-run-2ggqv -f -n jegan
(jegan@tektutor.org)$ tkn taskrun logs hello-task-with-params-run-2ggqv -f -n jegan
[echo] Hello Task with Params

(jegan@tektutor.org)$ oc get po
NAME                                           READY   STATUS      RESTARTS   AGE
hello-run-g9fcb-pod                            0/1     Completed   0          68m
hello-task-with-multiple-steps-run-qjcl2-pod   0/3     Completed   0          18m
hello-task-with-params-run-2ggqv-pod           0/1     Completed   0          16s
hello-task-with-params-run-szvfj-pod           0/1     Completed   0          53s
nginx-sample-nginx-6986d4c448-4m7hj            1/1     Running     0          3h
nginx-sample-nginx-6986d4c448-5pzx5            1/1     Running     0          3h
nginx-sample-nginx-6986d4c448-89wbp            1/1     Running     0          3h2m
nginx-sample-nginx-6986d4c448-8wcvn            1/1     Running     0          3h
nginx-sample-nginx-6986d4c448-mxlcm            1/1     Running     0          3h
</pre>
