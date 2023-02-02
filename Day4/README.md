# Day 4

## Lab - Deploying an application from a local git repository folder that has Dockerfile and java source
In this case, OpenShift will use docker strategy as our source repo has Dockerfile in it.

```
cd ~
git clone https://github.com/tektutor/spring-ms.git
cd spring-ms
oc new-app .
```

Checking the build log in real-time
```
oc logs -f bc/spring-ms
```

Once the build completes successfully, you can create a route as shown below
```
oc expose svc/spring-ms
oc get route
```
You may curl the route url or click on the route from Developer Topology up arrow and access the application.


## Lab - Deploying an application using GitHub Repo url that has just source code
In this case, we will have to instruct Openshift to use source strategy as we haven't supplied a Dockerfile along side the application source code.

```
oc new-app --name=hello registry.redhat.io/ubi8/openjdk-11~https://github.com/tektutor/openshift-springboot-s2i.git --allow-missing-images --strategy=source
```

Checking the build real-time logs
```
oc logs -f bc/hello
```

## Lab - Generating deployment declarative script 
```
oc create deploy nginx --image=bitnami/nginx --replicas=3 -o yaml --dry-run=client > nginx-deploy.yml
```

## Lab - Creating a deployment within OpenShift in declarative style
The create command only works the first time, while the apply command works always.  Generally apply is used when changes are made to an already existing resource within OpenShift.  However, people tend to use apply all the time.

```
cd ~/tekton-jan-2023
git pull

cd Day4/declarative-scripts
oc create -f nginx-deploy.yml
```

You may now check if the deployment is created
```
oc get deploy,rs,po
```

## Lab - Generating a ClusterIP Internal Service declarative YAML source code
```
oc expose deploy/nginx --type=ClusterIP --port=8080 -o yaml --dry-run=client > nginx-clusterip-svc.yml
```

## Lab - Creating a ClusterIP Internal Service in declarative style
```
cd ~/tekton-jan-2023
git pull

cd Day4/declarative-scripts
oc apply -f nginx-clusterip-svc.yml
```

You could then, list and describe the service
```
oc get svc
oc describe svc/nginx
```

## Lab - Generating the declarative route source code
```
oc expose svc/nginx --dry-run=client -o yaml > nginx-route.yml
```

## Lab - Creating a route in declarative style
```
cd ~/tekton-jan-2023
git pull

cd Day4/declarative-scripts
oc apply -f nginx-route.yml
```

You may then, list the route and access the URL
```
oc get route
```
