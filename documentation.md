
##### Configure YAML extension to use kubernetes schema
```
{
    "redhat.telemetry.enabled": false,
    "yaml.schemas": {
        "kubernetes":"*.yml"
            
    }
}

```
##### Configure minikube to use docker as it's driver
```
deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ minikube start --driver=docker
😄  minikube v1.28.0 on Microsoft Windows 10 Home 10.0.19044 Build 19044
✨  Using the docker driver based on existing profile
👍  Starting control plane node minikube in cluster minikube
🚜  Pulling base image ...
🔄  Restarting existing docker container for "minikube" ...
🐳  Preparing Kubernetes v1.25.3 on Docker 20.10.20 ...
🔎  Verifying Kubernetes components...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
🌟  Enabled addons: storage-provisioner, default-storageclass
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```
##### Create pods directory for pod definition templates
```
mkdir pods
```
##### Create nginx.yml our nginx pods image

```
apiVersion: v1
kind: Pod
metadata:
  name: test
  labels:
      app: myapps
spec:
  containers:
    - name: nginx
      image: nginx
```

##### Next Let's create add ReplicaSets to our configuration
##### Create a folder replicasets and inside it, create a file replicaset.yml
```
mkdir replicasets

touch replicasets/replicaset.yml
```

##### in replicaset.yml
```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: myapp-replicaset
  labels:
    app: myapp
spec:    
```

##### under the spec section, we need to add the pod definition (without the apiversion and kind)
##### We shall use the nginx.yaml pod definition
##### The matchLabels label must be the same as the metadata label.
##### label on selector and pod must match as it is what ties them together

##### The updated replicaset.yml

```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: myapp-replicaset
  labels:
    app: myapp
    

spec:
  selector:
    matchLabels:
      app: myapps
    
  replicas: 3

  template:
    metadata:
      name: nginx
      labels:    
        app: myapps
        
    spec:
       containers:
       - name: nginx
         image: nginx

```

##### Now lets create the replicaset

```
kubectl create -f replicaset.yml
```

##### I initially got errors as the selector and pod labels didn't match


```
$ kubectl create -f replicaset.yml
replicaset.apps/myapp-replicaset created

$ kubectl get replicaset
NAME               DESIRED   CURRENT   READY   AGE
myapp-replicaset   3         3         3       3h25m

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-microservices-architecture/replicasets (main)
$ kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE
myapp-replicaset-2pkzg   1/1     Running   0          3h27m
myapp-replicaset-97xpg   1/1     Running   0          3h27m
myapp-replicaset-g2ng7   1/1     Running   0          3h27m

```

##### One replicaset comprised of 3 pods created succesfully

##### Let's delete one pod to see if the replicset will spin up another automatically

```
kubectl delete pod myapp-replicaset-2pkzg
```
##### The replicaset immediately starts creating another pod/container

```
$ kubectl delete pod myapp-replicaset-2pkzg
pod "myapp-replicaset-2pkzg" deleted

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-microservices-architecture/replicasets (main)
$ kubectl get pods
NAME                     READY   STATUS              RESTARTS   AGE
myapp-replicaset-47v2h   0/1     ContainerCreating   0          7s
myapp-replicaset-97xpg   1/1     Running             0          12h
myapp-replicaset-g2ng7   1/1     Running             0          12h
```


##### If we create a new pod with the same label as the replicaset, it will be terminated automatically if the maximum number of pods are running
Lets create another pod using the nginx.yml file and not the replicaset.yml file

```
kubectl create -f nginx.yml
```
##### Here is the result

```
$ kubectl get pods
NAME                     READY   STATUS        RESTARTS   AGE
myapp-replicaset-47v2h   1/1     Running       0          18m
myapp-replicaset-97xpg   1/1     Running       0          12h
myapp-replicaset-g2ng7   1/1     Running       0          12h
nginx-2                  1/1     Terminating   0          9s
```


##### The new pod is terminated


---

##### I will now scale the number of pods in the replica to 5
##### This can be done on the command line as shown below or by editing the replicaset.yml file and setting the replica value to 5

```
kubectl scale replicaset myapp-replicaset --replicas=5

```

##### check if scale succesful 
```
kubectl get replicaset
```
```
$ kubectl get replicaset
NAME               DESIRED   CURRENT   READY   AGE
myapp-replicaset   5         5         5       12h
```

##### I will display the created pods which are now 5
```

$ kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE  
myapp-replicaset-47v2h   1/1     Running   0          29m  
myapp-replicaset-97xpg   1/1     Running   0          12h  
myapp-replicaset-dd25k   1/1     Running   0          3m32s
myapp-replicaset-g2ng7   1/1     Running   0          12h  
myapp-replicaset-tlng2   1/1     Running   0          3m32s
```

##### To scale by editing the .yaml file
##### Let us scale pods to 6

```
kubectl edit replicaset myapp-replicaset
```
##### This opens a text file and I updated replicas to 6

```
$ kubectl get replicaset
NAME               DESIRED   CURRENT   READY   AGE
myapp-replicaset   6         6         6       12h
```
```
$ kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE
myapp-replicaset-47v2h   1/1     Running   0          38m
myapp-replicaset-97xpg   1/1     Running   0          12h
myapp-replicaset-dd25k   1/1     Running   0          12m
myapp-replicaset-f22hl   1/1     Running   0          3m46s
myapp-replicaset-g2ng7   1/1     Running   0          12h
myapp-replicaset-tlng2   1/1     Running   0          12m
```


##### We may also need to scale down our replicaset

```
kubectl scale replicaset myapp-replicaset --replicas=2
```

```
$ kubectl scale replicaset myapp-replicaset --replicas=2
replicaset.apps/myapp-replicaset scaled


$ kubectl get replicaset
NAME               DESIRED   CURRENT   READY   AGE
myapp-replicaset   2         2         2       12h
```



##### A ReplicaSet ensures that a number of Pods is created in a cluster. The pods are called replicas and are the mechanism of availability in Kubernetes. But changing the ReplicaSet will not take effect on existing Pods, so it is not possible easily change, for example, the image version.

##### A deployment is a higher abstraction that manages one or more ReplicaSets to provide controlled rollout of a new version. When the image version is changed in the Deployment, a new ReplicaSet for this version will be created with initially zero replicas. Then it will be scaled to one replica, after that is running, the old ReplicaSet will be scaled down. (The number of newly created pods, the step size so to speak, can be tuned.)

---

##### I will now create a deployment by creating a deployment definition file deployment-definition.yml inside a folder called deployment.

```
$ mkdir deployment

$ touch deployment/deployment-definition.yml

$ cd deployment

$ kubectl create -f deployment-definition.yml
deployment.apps/myapp-deployment created

```

##### Run commands to view created oblects

```
$ kubectl get deployment
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
myapp-deployment   3/3     3            3           66m

$ kubectl get replicaset
NAME                          DESIRED   CURRENT   READY   AGE
myapp-deployment-567f66cdb4   3         3         3       25s

   
$ kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
myapp-deployment-567f66cdb4-7pwdk   1/1     Running   0          38s
myapp-deployment-567f66cdb4-grgqz   1/1     Running   0          38s
myapp-deployment-567f66cdb4-xwbmc   1/1     Running   0          38s

```
###













deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl create -f replica-set.yml
error: resource mapping not found for name: "myapp-replicaset" namespace: "" from "replica-set.yml": no matches for kind "ReplicaSet" in version "app/v1"
ensure CRDs are installed first

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl create -f replica-set.yml
The ReplicaSet "myapp-replicaset" is invalid: spec.template.metadata.labels: Invalid value: map[string]string{"app":"myapp", "tier":"frontend"}: `selector` does not match template `labels`

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl create -f replica-set.yml
The ReplicaSet "myapp-replicaset" is invalid: spec.template.metadata.labels: Invalid value: map[string]string{"app":"myapp", "tier":"frontend"}: `selector` does not match template `labels`

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl create -f replica-set.yml
replicaset.apps/myapp-replicaset created

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl get replicasets
NAME               DESIRED   CURRENT   READY   AGE
myapp-replicaset   3         3         3       49s

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl get pods
NAME                     READY   STATUS    RESTARTS      AGE
myapp-replicaset-fxfmf   1/1     Running   0             78s
myapp-replicaset-q8mb4   1/1     Running   0             78s
myapp-replicaset-tkxwj   1/1     Running   0             78s
nginx                    1/1     Running   1 (12m ago)   12h

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl detele pod myapp-replicaset-fxfmf
error: unknown command "detele" for "kubectl"

Did you mean this?
        delete

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl delete pod myapp-replicaset-fxfmf
Unable to connect to the server: dial tcp 127.0.0.1:64949: connectex: No connection could be made because the target machine actively refused it.

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl delete pod myapp-replicaset-fxfmf
Unable to connect to the server: dial tcp 127.0.0.1:64949: connectex: No connection could be made because the target machine actively refused it.

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl delete pod myapp-replicaset-fxfmf
Unable to connect to the server: dial tcp 127.0.0.1:64949: connectex: No connection could be made because the target machine actively refused it.

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ 
 *  History restored 


deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ minikube start --driver=docker
😄  minikube v1.28.0 on Microsoft Windows 10 Home 10.0.19044 Build 19044
✨  Using the docker driver based on existing profile
👍  Starting control plane node minikube in cluster minikube
🚜  Pulling base image ...
🔄  Restarting existing docker container for "minikube" ...
🐳  Preparing Kubernetes v1.25.3 on Docker 20.10.20 ...
🔎  Verifying Kubernetes components...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
🌟  Enabled addons: storage-provisioner, default-storageclass
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl create -f replica-set.yml
error: resource mapping not found for name: "myapp-replicaset" namespace: "" from "replica-set.yml": no matches for kind "ReplicaSet" in version "app/v1"
ensure CRDs are installed first

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl create -f replica-set.yml
The ReplicaSet "myapp-replicaset" is invalid: spec.template.metadata.labels: Invalid value: map[string]string{"app":"myapp", "tier":"frontend"}: `selector` does not match template `labels`

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl create -f replica-set.yml
The ReplicaSet "myapp-replicaset" is invalid: spec.template.metadata.labels: Invalid value: map[string]string{"app":"myapp", "tier":"frontend"}: `selector` does not match template `labels`

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl create -f replica-set.yml
replicaset.apps/myapp-replicaset created

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl get replicasets
NAME               DESIRED   CURRENT   READY   AGE
myapp-replicaset   3         3         3       49s

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl get pods
NAME                     READY   STATUS    RESTARTS      AGE
myapp-replicaset-fxfmf   1/1     Running   0             78s
myapp-replicaset-q8mb4   1/1     Running   0             78s
myapp-replicaset-tkxwj   1/1     Running   0             78s
nginx                    1/1     Running   1 (12m ago)   12h

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl detele pod myapp-replicaset-fxfmf
error: unknown command "detele" for "kubectl"

Did you mean this?
        delete

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl delete pod myapp-replicaset-fxfmf
Unable to connect to the server: dial tcp 127.0.0.1:64949: connectex: No connection could be made because the target machine actively refused it.

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl delete pod myapp-replicaset-fxfmf
Unable to connect to the server: dial tcp 127.0.0.1:64949: connectex: No connection could be made because the target machine actively refused it.

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl delete pod myapp-replicaset-fxfmf
Unable to connect to the server: dial tcp 127.0.0.1:64949: connectex: No connection could be made because the target machine actively refused it.

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ 
 *  History restored 


deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ minikube start --driver=docker
😄  minikube v1.28.0 on Microsoft Windows 10 Home 10.0.19044 Build 19044
✨  Using the docker driver based on existing profile
👍  Starting control plane node minikube in cluster minikube
🚜  Pulling base image ...
🔄  Restarting existing docker container for "minikube" ...
🐳  Preparing Kubernetes v1.25.3 on Docker 20.10.20 ...
🔎  Verifying Kubernetes components...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
🌟  Enabled addons: default-storageclass
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl get pods
NAME                     READY   STATUS    RESTARTS      AGE
myapp-replicaset-fxfmf   1/1     Running   1 (17h ago)   18h
myapp-replicaset-q8mb4   1/1     Running   1 (17h ago)   18h
myapp-replicaset-tkxwj   1/1     Running   1 (17h ago)   18h
nginx                    1/1     Running   2 (17h ago)   30h

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl delete pod myapp-replicaset-fxfmf
pod "myapp-replicaset-fxfmf" deleted

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl get pods
NAME                     READY   STATUS    RESTARTS      AGE
myapp-replicaset-67l2r   1/1     Running   0             6s
myapp-replicaset-q8mb4   1/1     Running   1 (17h ago)   18h
myapp-replicaset-tkxwj   1/1     Running   1 (17h ago)   18h
nginx                    1/1     Running   2 (17h ago)   30h

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl get pods
NAME                     READY   STATUS    RESTARTS      AGE
myapp-replicaset-67l2r   1/1     Running   0             29s
myapp-replicaset-q8mb4   1/1     Running   1 (17h ago)   18h
myapp-replicaset-tkxwj   1/1     Running   1 (17h ago)   18h
nginx                    1/1     Running   2 (17h ago)   30h

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl create -f nginx.yml
pod/test2 created

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl get pods
NAME                     READY   STATUS    RESTARTS      AGE
myapp-replicaset-67l2r   1/1     Running   0             9m22s
myapp-replicaset-q8mb4   1/1     Running   1 (17h ago)   18h
myapp-replicaset-tkxwj   1/1     Running   1 (17h ago)   18h
nginx                    1/1     Running   2 (17h ago)   30h

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl create -f nginx.yml
pod/test created

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl get pods
NAME                     READY   STATUS    RESTARTS      AGE
myapp-replicaset-67l2r   1/1     Running   0             10m
myapp-replicaset-q8mb4   1/1     Running   1 (17h ago)   18h
myapp-replicaset-tkxwj   1/1     Running   1 (17h ago)   18h
nginx                    1/1     Running   2 (17h ago)   30h

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl delete pod nginx
pod "nginx" deleted

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl get pods
NAME                     READY   STATUS    RESTARTS      AGE
myapp-replicaset-67l2r   1/1     Running   0             11m
myapp-replicaset-q8mb4   1/1     Running   1 (17h ago)   18h
myapp-replicaset-tkxwj   1/1     Running   1 (17h ago)   18h

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl create -f nginx.yml
pod/test created

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl get pods
NAME                     READY   STATUS    RESTARTS      AGE
myapp-replicaset-67l2r   1/1     Running   0             12m
myapp-replicaset-q8mb4   1/1     Running   1 (17h ago)   18h
myapp-replicaset-tkxwj   1/1     Running   1 (17h ago)   18h

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl get pods
NAME                     READY   STATUS    RESTARTS      AGE
myapp-replicaset-67l2r   1/1     Running   0             13m
myapp-replicaset-q8mb4   1/1     Running   1 (17h ago)   18h
myapp-replicaset-tkxwj   1/1     Running   1 (17h ago)   18h

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl create -f nginx.yml
pod/test created

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl get pods7
error: the server doesn't have a resource type "pods7"

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl get pods
NAME                     READY   STATUS    RESTARTS      AGE
myapp-replicaset-67l2r   1/1     Running   0             13m
myapp-replicaset-q8mb4   1/1     Running   1 (17h ago)   18h
myapp-replicaset-tkxwj   1/1     Running   1 (17h ago)   18h

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl create -f nginx.yml
pod/test created

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl get pods
NAME                     READY   STATUS        RESTARTS      AGE
myapp-replicaset-67l2r   1/1     Running       0             14m
myapp-replicaset-q8mb4   1/1     Running       1 (17h ago)   18h
myapp-replicaset-tkxwj   1/1     Running       1 (17h ago)   18h
test                     0/1     Terminating   0             2s

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl get pods
NAME                     READY   STATUS    RESTARTS      AGE
myapp-replicaset-67l2r   1/1     Running   0             14m
myapp-replicaset-q8mb4   1/1     Running   1 (17h ago)   18h
myapp-replicaset-tkxwj   1/1     Running   1 (17h ago)   18h

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-deploy-pod
$ kubectl describe replicaset myapp-replicaset
Name:         myapp-replicaset
Namespace:    default
Selector:     app=myapps
Labels:       app=myapp
Annotations:  <none>
Replicas:     3 current / 3 desired
Pods Status:  3 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  app=myapps
  Containers:
   nginx:
    Image:        nginx
    Port:         <none>
    Host Port:    <none>
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Events:
  Type    Reason            Age                 From                   Message
  ----    ------            ----                ----                   -------
  Normal  SuccessfulCreate  18h                 replicaset-controller  Created pod: myapp-replicaset-fxfmf
  Normal  SuccessfulCreate  18h                 replicaset-controller  Created pod: myapp-replicaset-tkxwj
  Normal  SuccessfulCreate  18h                 replicaset-controller  Created pod: myapp-replicaset-q8mb4
  Normal  SuccessfulCreate  15m                 replicaset-controller  Created pod: myapp-replicaset-67l2r
  Normal  SuccessfulDelete  6m22s               replicaset-controller  Deleted pod: test2
  Normal  SuccessfulDelete  83s (x4 over 5m6s)  replicaset-controller  Deleted pod: test
```