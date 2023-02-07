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

Now, let's create a nginx.yml file with below content
<pre>
Create an yaml file named nginx.yml with below content
<pre>
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
