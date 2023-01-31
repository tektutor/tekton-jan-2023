# Day 1

## What is dual/multi booting?
- boot loader utility
  - is a tiny 512 kilo bytes system utility that get installed in Master Boot Record(MBR) in the hard disk
  - when the laptop/desktop is booted, after BIOS Power On Self Test(POST) completes, the BIOS instructs CPU to run the boot loader
  - the boot loader then scans the hard disk looking for OS, it then gives a menu list the Operating Systems for us to choose
- in this fashion, only one OS can be active at any point of time, though you may have installed two or more OS in the same

## Virtualization Technology
- aka Hypervisor
- consolidates many physical servers into into a single server
- software + hardware technology
- Processors that supports Virtualization has a CPU feature set for Virtualization
  AMD 
   - Virtualization Feature is called AMD-V
  Intel
   - Virtualization Feature is called VT-X
- Two types
  - Type 1 (Bare Metal Hypervisors) and 
    - They can be installed directly on the hardware without installing OS
    - Used in Servers & Workstations
    - examples
      - VMWare vSphere(v-center)
  - Type 2
    - They are used in Desktops/Laptops/Workstations
    - Examples
      - VMWare
        - Fusion (Mac OS-X)
        - Workstation ( Linux/Windows)
      - Oracle VirtualBox
      - Parallels (Mac OS-X)

## What is Virtual Machine?
- is a technology that allows running multiples OS on the same desktop/laptop/workstation/server
- many OS can be active at the same time unlike the boot loader which allows only one OS active
- each Virtual Machine needs to be allocated with dedicated hardware resources, hence it is called as a heavy-weight virtualization technology

## What are the factors that controls/decides the maximum Virtual Machines a server can support?
- Total CPU Cores supported in the Processor(s)
- RAM
- Disk Size

## Linux Kernel Features that enables Container Technology
1. Namespace 
   - helps us isolate one application process from other application running on the same OS
2. Control Groups(CGroups)
   - helps us apply resource quota restrictions like
     - how many CPU cores an application can use
     - how much RAM an application can use
     - how much Disk space an application can use

## What is namespace?
- Container technology using this namespace isolates one container from another container

## What is a Container?
- normal application process that runs in a separate namespace
- is a sandbox environment which isolates your application using a namespace
- self-contained services with all dependencies bundled within a container
- one container represents one application (legacy application, it could be a REST/SOAP/WebService, Microservice, Server application like DB Server, Web Server or App Server,etc )
- the application that is containerized will also have all the dependencies of the application
- is lightweight application virtualization technology
  - containers don't require dedicated hardwares unlike Virtual machines
  - containers are application process while Virtual Machine is a fully functional Operating System

## Container Tools
- LXC
- Containerd
- Docker
- Podman

## What is Container Runtime?
- Container Runtimes are the softwares that manages containers
  - can create a container 
  - run the container
  - stop the container
  - restart the container
  - delete the container
  - abort/kill the container
- Example
  - runC
  - CRI-O

## What is a Container Engine?
- high-level software that uses Container Runtime under the hood to manage containers
- similarly they depend on other tools to manage container images
- it's user-friendly tool that provides easy to use command
- without knowing low-level details we can manage images and container with this tool
- Examples
  - Docker & Podman

## What is a Container Orchestration Tool? 
- manages containerized application (REST/SOAP API, microservices, server application, legacy self-container appliction)
- benefits of this tool
  - helps in making your applications Highly Available(HA)
  - supports in-built application monitoring features (health-check, live check)
  - in case your application stops responding or it crashes, it automatically starts another instance of your application
  - helps in scaling up/down your application on demand depending user traffic or some application performance metrics
  - supports rolling update
     - helps in upgrading/downgrading your application version from one to other version without any downtime
  - also helps you create internal only service or external service to expose your application over Internet
  - helps in using external storage for your applications
- Examples
  - Docker SWARM
  - Google Kubernetes
    - supports Custom Resource Definitions aka CRDs to add new additional features
    -  supports Operators to manage your Custom Resource (new features)
    -  open source
    -  primarily console based (CLI)
    -  supports many different types of Container Runtimes that implement CRI(Container Runtime Interface)
  - Red Hat OpenShift ( developed on top of Kubernetes )
    - backed by Red Hat ( an IBM company )
    - world-wide support can be expected unlike Kubernetes
    - supports both CLI and Web console
    - supports CRI-O container runtime and Podman Container Engine only
 
## Installing Code Ready Containers (CRC) on your Windows/Linux/Mac Laptop
```
https://developers.redhat.com/blog/2020/09/09/install-red-hat-openshift-operators-on-your-laptop-using-red-hat-codeready-containers-and-red-hat-marketplace#step_1__install_codeready_containers
```

## Control Plane Components
- they run only on the master nodes or nodes that has master role
- Control Plane Components
  - API Server
  - etcd database
  - Scheduler
  - Controller Managers

### API Server
- this implements all the Kubernetes/OpenShift functionalities as REST API
- this stores all the cluster and application status on to the etcd datastore
- this is the only component that has direct access to etcd database
- API server triggers specific events each time there is an update in the etcd database

### etcd database
- it is an opensource key/value datastore
- it is not implemented by Kubernetes/OpenShift team, this is an independent opensource project used by Kubernetes/OpenShift
- it is capable of working as a cluster, hence when one instance of etcd db is updated, all the other etcd databases within the cluster will be synchronized automatically

### Scheduler
- this is the component that identifies a healthy node where an application can be deployed

### Controller Managers
- this is a collection of many Controllers whose primary function is to monitor and heal them when required
- this is one which make your application deployments High Available(HA)
- There are many inbuilt controllers
  - Deployment Controller
  - ReplicaSet Controller
  - Node Controller
  - Endpoint Controller

## What are the Kubernetes Resources
- Deployment
- ReplicaSet
- Pod
- DaemonSet
- Job
- StatefulSet
- Namespace
- PersistentVolume
- PersistentVolumeClaim
- Service
- Ingress
- EndPoint

## What are the OpenShift Resources
- Route (Custom Resource added by OpenShift )
- DeploymentConfig  (Custom Resource added by OpenShift )
- BuildConfig (Custom Resource added by OpenShift )

## What is a Deployment?
- This represents your application deployed within Kubernetes/OpenShift
- this has a name and number of application instances and their status
- Under a deployment, you also have something called ReplicaSet
- This resource is managed by Deployment Controller
- Supports rolling update and facilitates scale up/down via ReplicaSet

## What is a ReplicaSet?
- this manages the application Pods
- this supports scaling up/down based on user traffic to your applications
- This resource is managed by ReplicaSet Controller

## What is a Pod?
- English literal meaning - group of Whales, Whale is the Docker logo
- a group of related containers is called a Pod
- as per recommended best practice, one container per Pod is good
- technically multiples containers can be part of a single Pod
- Pods are created by kubelet agent that runs in every node based on events received from API Server
- Scheduler decides where each Pod can be deployed
- ReplicaSet Controller ensures the desired number of Pods are alive, when it finds less/more than desired Pod it takes action to ensure the desired number of Pods and actual number of Pods instances matches always

## OpenShift Commands

### List the OpenShift cluster nodes
Each node could be a Virtual Machine, an EC2 instance on the Cloud or a Physical Server.  Starting from OpenShift 4.x, Master nodes only supports RedHat Enterprise Core OS while Worker nodes can choose between RedHat Enterprise Core OS or Red Hat Enterprise Linux(RHEL).

Red Hat Enterprise Core OS is a small foot-print Operating System that is optimized for Containerized applications and Container Orchestration Platforms.

```
oc get nodes
```





### List the nodes in wide mode which shows more details like IP address, etc.,
```
oc get nodes -o wide
```

### List the master-1 node using label as a selector
```
oc get nodes -l kubernetes.io/hostname=master-1.ocp.alchemy.com
```

### Listing all pods in the default namespace
```
oc get pods
```

### Listing all pods in all the namespaces
```
oc get pods --all-namespaces
```

### Listing all api-servers (control-plane component)
```
oc get pods --all-namespaces | grep apiserver*
```

### Listing all etcd (control-plane component)
```
oc get pods --all-namespaces | grep etcd*
```

### Listing all controller (control-plane component)
```
oc get pods --all-namespaces | grep controller*
```

### Listing all scheduler (control-plane component)
```
oc get pods --all-namespaces | grep scheduler*
```

### Getting help info about Deployment resource
```
oc explain deployment
```

### Getting help info about Replicaset resource
```
oc explain replicaset
```

### Getting help info about pod resource
```
oc explain pod
```
