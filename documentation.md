
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