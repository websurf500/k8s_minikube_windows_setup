## YouTube Video walkthough
Follow this tutorial on YouTube here: https://youtu.be/ejLQ5danlIY

## Kubernetes: What is it?
Kubernetes is a portable, extensible, open-source platform for managing containerized workloads and services that facilitates
both declarative configuration and automation.

Source: https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/


## Why do I need it?
Containers are a good way to bundle and run your applications. In a production environment, you need to manage the containers
that run the applications and ensure that there is no downtime. For example, if a container goes down, another container needs
to start. Wouldn’t it be easier if this behaviour was handled by a system?

That’s how Kubernetes comes to the rescue! Kubernetes provides you with a framework to run distributed systems resiliently.
It takes care of scaling and failover for your application, provides deployment patterns, and more.


## Introduction to minikube
Minikube is a tool that makes it easy to run Kubernetes locally. Minikube runs a single-node Kubernetes cluster inside a
Virtual Machine (VM) on your laptop for users looking to try out Kubernetes or develop with it day-to-day.

In this example, we’ll use our laptop along with Minikube, VirtualBox and Kubectl to create a single node Kubernetes cluster:
(Source picture:Platform9: https://platform9.com/docs/install-kubernetes-the-ultimate-guide/)

# Overview:

I'm assuming you have Virtualbox already setup. If you need a demo on any of these tools, just let me know in the comments.

Install VirtualBox on your system:
https://www.virtualbox.org/wiki/Downloads
 
 
### Minikube install:
On windows, MAC OS or Linux, download and install minikube from the following link:
https://minikube.sigs.k8s.io/docs/start/

Follow the install procedure first and once complete, continue with these steps.

### Start minikube
Minikube gets installed in the ```C:\``` so find the location in file explorer and open up a CMD windows in that directory.

The first install takes a few minutes:

```
C:\minikube>minikube start
C:\minikube>minikube start
* minikube v1.28.0 on Microsoft Windows 11 Home 10.0.22000 Build 22000
* Automatically selected the virtualbox driver
* Downloading VM boot image ...
    > minikube-v1.28.0-amd64.iso....:  65 B / 65 B [---------] 100.00% ? p/s 0s
    > minikube-v1.28.0-amd64.iso:  274.45 MiB / 274.45 MiB  100.00% 7.62 MiB p/
* Starting control plane node minikube in cluster minikube
* Downloading Kubernetes v1.25.3 preload ...
    > preloaded-images-k8s-v18-v1...:  385.44 MiB / 385.44 MiB  100.00% 7.60 Mi
* Creating virtualbox VM (CPUs=2, Memory=6000MB, Disk=20000MB) ...
! This VM is having trouble accessing https://registry.k8s.io
* To pull new external images, you may need to configure a proxy: https://minikube.sigs.k8s.io/docs/reference/networking/proxy/
* Preparing Kubernetes v1.25.3 on Docker 20.10.20 ...
  - Generating certificates and keys ...
  - Booting up control plane ...
  - Configuring RBAC rules ...
  - Using image gcr.io/k8s-minikube/storage-provisioner:v5
* Verifying Kubernetes components...
* Enabled addons: storage-provisioner, default-storageclass
* kubectl not found. If you need it, try: 'minikube kubectl -- get pods -A'
* Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```

### Kubectl install:
Kubectl is part of the minikube install so you can run kubectl commands just by putting minikube infront of it.

**But** we're going to install kubectl because we want full functionality.

On windows, MAC OS or Linux, download and install kubectl from the following link:
https://kubernetes.io/docs/tasks/tools/install-kubectl/

Find your OS and follow the install process. I ran this command from C:\minikube

```
C:\minikube>curl.exe -LO "https://dl.k8s.io/release/v1.25.0/bin/windows/amd64/kubectl.exe"
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   138  100   138    0     0    123      0  0:00:01  0:00:01 --:--:--   423
100 43.6M  100 43.6M    0     0  6420k      0  0:00:06  0:00:06 --:--:-- 7793k
```

### Kubernetes commands
Check kubectl commands work:

```
C:\minikube>kubectl version
WARNING: This version information is deprecated and will be replaced with the output from kubectl version --short.  Use --output=yaml|json to get the full version.
Client Version: version.Info{Major:"1", Minor:"25", GitVersion:"v1.25.0", GitCommit:"a866cbe2e5bbaa01cfd5e969aa3e033f3282a8a2", GitTreeState:"clean", BuildDate:"2022-08-23T17:44:59Z", GoVersion:"go1.19", Compiler:"gc", Platform:"windows/amd64"}
Kustomize Version: v4.5.7
Server Version: version.Info{Major:"1", Minor:"25", GitVersion:"v1.25.3", GitCommit:"434bfd82814af038ad94d62ebe59b133fcb50506", GitTreeState:"clean", BuildDate:"2022-10-12T10:49:09Z", GoVersion:"go1.19.2", Compiler:"gc", Platform:"linux/amd64"}

C:\minikube>kubectl get pods -A
NAMESPACE     NAME                               READY   STATUS    RESTARTS        AGE
kube-system   coredns-565d847f94-5zzq5           1/1     Running   0               7m2s
kube-system   etcd-minikube                      1/1     Running   0               7m15s
kube-system   kube-apiserver-minikube            1/1     Running   0               7m15s
kube-system   kube-controller-manager-minikube   1/1     Running   0               7m15s
kube-system   kube-proxy-zw9qz                   1/1     Running   0               7m2s
kube-system   kube-scheduler-minikube            1/1     Running   0               7m15s
kube-system   storage-provisioner                1/1     Running   1 (6m31s ago)   7m13s
```

The kubectl command creates a deployment and deployments create our pods and keep them up and running. Lets add the meetup-app into minukube:

```
C:\minikube>kubectl create deployment meetup-app --image=dmccuk/meetup-app:first --port=80
deployment.apps/meetup-app created

C:\minikube>kubectl get deployment
NAME         READY   UP-TO-DATE   AVAILABLE   AGE
meetup-app   1/1     1            1           12s

C:\minikube>kubectl get pods
NAME                         READY   STATUS    RESTARTS   AGE
meetup-app-9f6b86b86-l5xm8   1/1     Running   0          21s
```

Lets check out the deployment configuration by checking the yaml file:

```
C:\minikube>kubectl get deployment -o yaml
apiVersion: v1
items:
- apiVersion: apps/v1
  kind: Deployment
  metadata:
    annotations:
      deployment.kubernetes.io/revision: "1"
    creationTimestamp: "2022-11-17T11:51:16Z"
    generation: 1
    labels:
      app: meetup-app
    name: meetup-app
    namespace: default
<results omitted>
```

Now it’s running we can expose it outside of Kubernetes:

```
C:\minikube>kubectl expose deployment meetup-app --type=NodePort
service/meetup-app exposed
```

Other options are available to expose your deployment. These include Port-forwarding, NodePort & Load-balancer.

Once we expose out app, we can check the service. There is our exposed IP address:

```
C:\minikube>kubectl get service
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP        99m
meetup-app   NodePort    10.97.162.135   <none>        80:30219/TCP   19s
```

### Now get the URL and check it works:

```
C:\minikube>minikube service meetup-app --url
http://192.168.59.100:30219
```

** You can visit the service on in your browser!**

### Let’s scale out application:

```
C:\minikube>kubectl scale deployment meetup-app --replicas=3
deployment.apps/meetup-app scaled
```

And to prove it:

```
C:\minikube>kubectl get po
NAME                         READY   STATUS    RESTARTS   AGE
meetup-app-9f6b86b86-8vxsb   1/1     Running   0          15s
meetup-app-9f6b86b86-l5xm8   1/1     Running   0          3m18s
meetup-app-9f6b86b86-nhfcf   1/1     Running   0          15s
```

We can also get the individual end points:

```
C:\minikube>kubectl get ep meetup-app
NAME         ENDPOINTS                                   AGE
meetup-app   172.17.0.3:80,172.17.0.4:80,172.17.0.5:80   2m13s

C:\minikube>kubectl get po -o wide
NAME                         READY   STATUS    RESTARTS   AGE     IP           NODE       NOMINATED NODE   READINESS GATES
meetup-app-9f6b86b86-8vxsb   1/1     Running   0          45s     172.17.0.4   minikube   <none>           <none>
meetup-app-9f6b86b86-l5xm8   1/1     Running   0          3m48s   172.17.0.3   minikube   <none>           <none>
meetup-app-9f6b86b86-nhfcf   1/1     Running   0          45s     172.17.0.5   minikube   <none>           <none>
```

Testing: If we delete 2 of the pods, what’s going to happen?

```
C:\minikube>kubectl delete po  meetup-app-9f6b86b86-l5xm8 meetup-app-9f6b86b86-nhfcf
pod "meetup-app-9f6b86b86-l5xm8" deleted
pod "meetup-app-9f6b86b86-nhfcf" deleted

C:\minikube>kubectl get po
NAME                         READY   STATUS    RESTARTS   AGE
meetup-app-9f6b86b86-8vxsb   1/1     Running   0          87s
meetup-app-9f6b86b86-mxc8v   1/1     Running   0          11s
meetup-app-9f6b86b86-snn66   1/1     Running   0          11s
```

** They get recreated again!!**

### Now let go to the service URL via minikube:

```
C:\minikube>minikube service meetup-app --url
http://192.168.59.100:30219

C:\minikube>minikube service meetup-app
|-----------|------------|-------------|-----------------------------|
| NAMESPACE |    NAME    | TARGET PORT |             URL             |
|-----------|------------|-------------|-----------------------------|
| default   | meetup-app |          80 | http://192.168.59.100:30219 |
|-----------|------------|-------------|-----------------------------|
* Opening service default/meetup-app in default browser...
 
C:\minikube>kubectl get svc
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP        103m
meetup-app   NodePort    10.97.162.135   <none>        80:30219/TCP   4m32s

C:\minikube>kubectl describe svc meetup-app
Name:                     meetup-app
Namespace:                default
Labels:                   app=meetup-app
Annotations:              <none>
Selector:                 app=meetup-app
Type:                     NodePort
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       10.97.162.135
IPs:                      10.97.162.135
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  30219/TCP
Endpoints:                172.17.0.4:80,172.17.0.5:80,172.17.0.6:80
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
```

### Let checkout the dashboard:

```
C:\minikube>minikube dashboard
* Enabling dashboard ...
  - Using image docker.io/kubernetesui/dashboard:v2.7.0
  - Using image docker.io/kubernetesui/metrics-scraper:v1.0.8
* Some dashboard features require the metrics-server addon. To enable all features please run:

        minikube addons enable metrics-server

* Verifying dashboard health ...
* Launching proxy ...
* Verifying proxy health ...
* Opening http://127.0.0.1:56996/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/ in your default browser...
```

**A browser will open and you will see the cluster and information displayed on the webpage. Take a look around.**

Either close the browser or press “Ctrl + c” to close it from the command line. 


## What have you accomplished?

  * Built a docker container for a static website.
  * Pushed your container to the Docker hub
  * Installed and setup minikube and kubectl on you Laptop
  * Started a Kubernetes cluster an ran the container image
  * Exposed the container deployment and port
  * Used minikube to start the container as a Kubernetes service adding replicas
  * Finally started up the Kubernetes dashboard to display information about our cluster and pod.

## Delete deployment and service:

Now that we’ve used Kubernetes to run out container, we can delete the deployment and service.

First, let’s get all the information from Kubernetes that we need to delete it:

```
C:\minikube>kubectl get all
NAME                             READY   STATUS    RESTARTS   AGE
pod/meetup-app-9f6b86b86-8vxsb   1/1     Running   0          5m49s
pod/meetup-app-9f6b86b86-mxc8v   1/1     Running   0          4m33s
pod/meetup-app-9f6b86b86-snn66   1/1     Running   0          4m33s

NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
service/kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP        106m
service/meetup-app   NodePort    10.97.162.135   <none>        80:30219/TCP   7m31s

NAME                         READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/meetup-app   3/3     3            3           8m52s

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/meetup-app-9f6b86b86   3         3         3       8m52s
```

Now, run this command:

```
C:\minikube>kubectl delete deploy/meetup-app svc/meetup-app
deployment.apps "meetup-app" deleted
service "meetup-app" deleted
```

All that’s left is the minikube cluster.

```
C:\minikube>kubectl get all
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   107m
```

Lets stop Minikube:

```
C:\minikube>minikube stop
* Stopping node "minikube"  ...
* 1 node stopped.
```

**All done! Great work.**

## Next Steps
If you#re looking to get into learning kubernetes in more detail, I can highly recommend James Spurin. Checkout his Youtube channel here: https://www.youtube.com/@DiveInto

This was just a quick introduction into Docker and Kubernetes. I hope you had some fun.

Good luck in your Journey!