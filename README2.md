# Kubernetes
Created Saturday 28 December 2019
## Access dashboard after setting:

```

  https://<your master ip>/api/v1/proxy/namespaces/kube-system/services/kubernetes-dashboard

```
## Docker persistent storage:

##### Storage Testing:
**Loop-LVM** :for making files on local disk,(physical or virtual) act as strogage devices for the containers. useful for checking the compatibility issues and driver library issues.

##### Storage Live Implementation,(Production):
**direct-lvm** Once all problems have been ioned out, the progress to building live systems should only be implemented vi direct-lvm. Both systems use block access mode.
 
The two moder use snapshot and thinprovisioning for container technology framework, components of that can be found in the Linux Kernel.

Use Docker save to save container later on to an image, can use squash to make an image thinner.

```
<https://docs.docker.com/storage/storagedriver/device-mapper-driver/>

<https://docs.docker.com/storage/storagedriver/device-mapper-driver/#configure-direct-lvm-mode-for-production>


<https://docs.docker.com/storage/storagedriver/device-mapper-driver/#configure-direct-lvm-mode-manually>

```


 ## Kubernetes network:



* 3 Types
* Bridge: Similar to the bridge network standard.
* Overlay: For multi host communication, (weave).
* Macvlan: Most effieicnet, uses interface and sub interface of the host. Isolation and virtualization at a more efficient offering. Passing bridge to ensure isolation.


## Notes:
#### Weave:
3rd party operators such as weave network have already created their own docker network adaptation,(Plugins). Weave has the ability to utilize the through the network plugin, it also is compatible with network cni, draw back for it is overhead on packet encapsulation, this new attempt was added with Virtual Extensible Lan (VXLan) a vmware technology initially. This is a great improvement on performance

#### Flannel:
Comes with core os. some what defacto with Kubernetes, it is in most installs. It gives full subnet to each/ [host:Node.]() matches the Kubernetes, routable IP per Pod/group of containers. Include an in kernel vxlan encapsulation, for better performance. Has experimental multi network mode similar to overlay docker plugin. 

#### Calico:
 Layer 3 based networking model, propagate routes to virtual routes via BGP, as its low level access based and utilize Linux Kernel modules it can benefit from ACL, no need to overlay or virtual routes. this makes matters easier. more performant too. 

1- Service definition template:

```

apiVersion: v1 
kind: Service 
metadata: 
  name: node-js 
  labels: 
name: node-js 
spec: 
  type: LoadBalancer 
  ports: 
  - port: 80 
  selector: 
name: node-js

```

#### Node-js controller below:

```
apiVersion: v1 
kind: ReplicationController 
metadata: 
  name: node-js-labels 
  labels: 
name: node-js-labels 
app: node-js-express 
deployment: test 
spec: 
  replicas: 3 
  selector: 
name: node-js-labels 
app: node-js-express 
deployment: test 
  template: 
metadata: 
  labels: 
name: node-js-labels 
app: node-js-express 
deployment: test 
spec: 
  containers: 
  - name: node-js-labels 
image: jonbaier/node-express-info:latest 
ports: 
- containerPort: 80
```
#### Node-js-labels-service

```
apiVersion: v1 
kind: Service 
metadata: 
  name: node-js-labels 
  labels: 
name: node-js-labels 
app: node-js-express 
deployment: test 
spec: 
  type: LoadBalancer 
  ports: 
  - port: 80 
  selector: 
name: node-js-labels 
app: node-js-express 
deployment: test

```
#### Create the replication controller and service as follows:

```
$ kubectl create -f nodejs-labels-controller.yaml
$ kubectl create -f nodejs-labels-service.yaml

```
#### Note: According to digital ocean:


The very first time, you are deploying both kubernetes service(s) which provides the IP, and kubernetes pod(s) deployment(s).

Every time you deploy new changes, just change the pod deployment and reapply the changes with the kubectl, the service IP will stay unchanged, thus you can tie that IP address with the DO DNS.


The static ip (ingress):

####published in the Service’s .status.loadBalancer field. For example:

```
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
app: MyApp
  ports:
- protocol: TCP
  port: 80
  targetPort: 9376
  clusterIP: 10.0.171.239
  type: LoadBalancer
status:
  loadBalancer:
ingress:
- ip: 192.0.2.127

```

### Post Start and Pre stop ,(conditions):


```
apiVersion: v1 
kind: ReplicationController 
metadata: 
  name: apache-hook 
  labels: 
name: apache-hook 
spec: 
  replicas: 3 
  selector: 
name: apache-hook 
  template: 
metadata: 
  labels: 
name: apache-hook 
spec: 
  containers: 
  - name: apache-hook 
image: bitnami/apache:latest 
ports: 
- containerPort: 80 
lifecycle: 
  postStart: ## Here it comes
httpGet: 
  path: <http://my.registration-server.com/register/> 
  port: 80 
  preStop: ## Here it ends
exec: 
  command: ["/usr/local/bin/apachectl","-k","graceful-stop"]


```

## Replica sets: Replica sets are the new and improved version of replication controllers.

```

apiVersion: extensions/v1beta1 
kind: ReplicaSet 
metadata: 
  name: node-js-rs 
spec: 
  replicas: 3 
  selector: 
matchLabels: 
  app: node-js-express 
  deployment: test 
matchExpressions: 
  - {key: name, operator: In, values: [node-js-rs]} 
  template: 
metadata: 
  labels: 
name: node-js-rs 
app: node-js-express 
deployment: test 
spec: 
  containers: 
  - name: node-js-rs 
image: jonbaier/node-express-info:latest 
ports: 
- containerPort: 80

```


#### Replicaset VS Replicationcontroller:

```
apiVersion: extensions/v1beta1 
kind: ReplicaSet 
metadata: 
  name: node-js-rs 
spec: 
  replicas: 3 
  selector: 
matchLabels: 
  app: node-js-express 
  deployment: test 
matchExpressions: 
  - {key: name, operator: In, values: [node-js-rs]} 
  template: 
metadata: 
  labels: 
name: node-js-rs 
app: node-js-express 
deployment: test 
spec: 
  containers: 
  - name: node-js-rs 
image: jonbaier/node-express-info:latest 
ports: 
- containerPort: 80

# Listing 2-6: nodejs-labels-replicaset.yaml
```
## Replication controller:

```
apiVersion: v1 
kind: ReplicationController 
metadata: 
  name: node-js 
  labels: 
name: node-js 
spec: 
  replicas: 3 
  selector: 
name: node-js 
  template: 
metadata: 
  labels: 
name: node-js 
spec: 
  containers: 
  - name: node-js 
image: jonbaier/node-express-info:latest 
ports: 
- containerPort: 80 
livenessProbe: 
  # An HTTP health check  
  httpGet: 
path: /status/ 
port: 80 
  initialDelaySeconds: 30 
  timeoutSeconds: 1
# Listing 2-7: nodejs-health-controller.yaml

```

## Health Check example:


Kubernetes provides two layers of health checking. First, in the form of HTTP or TCP checks, K8s can attempt to connect to a particular endpoint and give a status of healthy on a successful connection. Second, application-specific health checks can be performed using command-line scripts.

Let's take a look at a few health checks in action. First, we'll create a new controller with a health check:

```
apiVersion: v1 
kind: ReplicationController 
metadata: 
  name: node-js 
  labels: 
name: node-js 
spec: 
  replicas: 3 
  selector: 
name: node-js 
  template: 
metadata: 
  labels: 
name: node-js 
spec: 
  containers: 
  - name: node-js 
image: jonbaier/node-express-info:latest 
ports: 
- containerPort: 80 
livenessProbe: 
  # An HTTP health check  
  httpGet: 
path: /status/ 
port: 80 
  initialDelaySeconds: 30 
  timeoutSeconds: 1

#Listing 2-7: nodejs-health-controller.yaml

```
# Note the addition of the livenessprobe element. This is our core health check element. From here, we can specify httpGet, tcpScoket, or exec. In #this example, we use httpGet to perform a simple check for a URI on our container. The probe will check the path and port specified and restart he #pod if it doesn't successfully return.

#Status codes between 200 and 399 are all considered healthy by the probe.
#Finally, initialDelaySeconds gives us the flexibility to delay health checks until the pod has finished initializing. The timeoutSeconds value is simply #the timeout value for the probe.

### Deployment
A new improved way over replication controllers, offer more flexible way of updating the apps:

```

apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: node-js-deploy
spec:
  minReplicas: 3
  maxReplicas: 6
  scaleTargetRef:
apiVersion: v1
kind: Deployment
name: node-js-deploy
  targetCPUUtilizationPercentage: 10
###Listing 5-3. node-js-deploy-hpa.yaml

```
### Jobs: An example, a job targets a container:

```
apiVersion: batch/v1
kind: Job
metadata:
  name: long-task
spec:
  template:
metadata:
  name: long-task
spec:
  containers:
  - name: long-task
image: docker/whalesay
command: ["cowsay", "Finishing that task in a jiffy"]
  restartPolicy: OnFailure
## Listing 5-5: longtask.yaml


Example for validation:

```
   kubectl describe jobs/long-task

```
Job Scheduler example:

```
apiVersion: batch/v2alpha1
kind: CronJob
metadata:
  name: long-task-cron
spec:
  schedule: "15 10 * * 6"
  jobTemplate:
spec:
  template:
spec:
  containers:
  - name: long-task-cron
image: docker/whalesay
command: ["cowsay", "Developers! Developers! Developers!
  \n\n Saturday task    
complete!"]
  restartPolicy: OnFailure
##Listing 5-6. longtask-cron.yaml
```

##  Daemon Set: Will run on every node in the cluster
```

apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  name: node-problem-detector-v0.1
  namespace: kube-system
  labels:
k8s-app: node-problem-detector
version: v0.1
kubernetes.io/cluster-service: "true"
spec:
  template:
metadata:
  labels:
  k8s-app: node-problem-detector
version: v0.1
kubernetes.io/cluster-service: "true"
spec:
  hostNetwork: true
  containers:
  - name: node-problem-detector
image: gcr.io/google_containers/node-problem-detector:v0.1
securityContext:
  privileged: true
resources:
  limits:
cpu: "200m"
memory: "100Mi"
  requests:
cpu: "20m"
memory: "20Mi"
volumeMounts:
- name: log
  mountPath: /log
  readOnly: true
volumes:
- name: log
  hostPath:
path: /var/log/

```

Storage volumes:

```

apiVersion: v1 
kind: Pod 
metadata: 
  name: memory-pd 
spec: 
  containers: 
  - image: nginx:latest 
ports: 
- containerPort: 80 
name: memory-pd 
volumeMounts: 
- mountPath: /memory-pd 
  name: memory-volume 
  volumes: 
  - name: memory-volume 
emptyDir: 
  medium: Memory 
#Listing 6-1: storage-memory.yaml


##### Operational commands

```
$ kubectl create -f storage-memory.yaml
$ kubectl exec memory-pd -- ls -lh | grep memory-pd

```


### Notes:

Check framework as code on **Glup.js**

Gulp example code for service:

```
var gulp = require('gulp'); 
var git = require('gulp-git'); 
var shell = require('gulp-shell'); 

// Clone a remote repo 
gulp.task('clone', function(){ 
  return git.clone('<https://github.com/jonbaierCTP/getting-started-with-kubernetes-se.git>', function (err) { 
if (err) throw err; 
  }); 

}); 

// Update codebase 
gulp.task('pull', function(){ 
  return git.pull('origin', 'master', {cwd: './getting-started-with-kubernetes-se'}, function (err) { 
if (err) throw err; 
  }); 
}); 

//Build Docker Image 
gulp.task('docker-build', shell.task([ 
  'docker build -t <your username>/node-gulp ./getting-started-with-kubernetes-se/docker-image-source/container-info/', 
  'docker push <your username>/node-gulp' 
])); 

//Run New Pod 
gulp.task('create-kube-pod', shell.task([ 
  'kubectl create -f node-gulp-controller.yaml', 
  'kubectl create -f node-gulp-service.yaml' 
])); 

//Update Pod 
gulp.task('update-kube-pod', shell.task([ 
  'kubectl delete -f node-gulp-controller.yaml'
```



* Useful commands: 


```
  sudo lsblk 
  sudo docker ps --format 'table ![](./README2_files/.Image)t![](./README2_files/.Status)'
  kubectl get events
  kubectl get service
   vagrant ssh node-1 -c 'sudo docker images'
eval $(kubelet docker-env -u)       
```
This command is very key on debuging docker

```
sudo docker ps --format 'table ![](./README2_files/.Image)t![](./README2_files/.Status)'

Scaling manually:

```
$ kubectl scale --replicas=3 rc/node-js-scale-b
$ kubectl scale --replicas=1 rc/node-js-scale-a
$ kubectl scale --replicas=4 rc/node-js-scale-b
$ kubectl scale --replicas=0 rc/node-js-scale-a

```
Getting a node from a pod for a job:

```
$ kubectl describe pod <Pod-Name-For-Job> | grep Node:

``` 
### Monitoring


* [Sysdig](<http://www.sysdigcloud.com>)
* [StackDriver](<http://stackdriver.com/>) 


