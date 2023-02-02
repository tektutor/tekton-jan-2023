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
