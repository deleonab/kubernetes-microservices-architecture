
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
##### Next, I will experiment with updates, upgrades and rollbacks in deployments
##### I have deleted all pods

```
$ kubectl get pods
No resources found in default namespace.
```
```
$ kubectl create -f deployment-definition.yml
deployment.apps/myapp-deployment created

$ kubectl rollout status deployment.apps/myapp-deployment
Waiting for deployment "myapp-deployment" rollout to finish: 0 of 3 updated replicas are available...
Waiting for deployment "myapp-deployment" rollout to finish: 1 of 3 updated replicas are available...
Waiting for deployment "myapp-deployment" rollout to finish: 2 of 3 updated replicas are available...
deployment "myapp-deployment" successfully rolled out
```

```
$ kubectl rollout history deployment.apps/myapp-deployment
deployment.apps/myapp-deployment 
REVISION  CHANGE-CAUSE
1         <none>
```
##### to record the change/cause
##### Delete the deployment and recreate with --record option then run the history command again

```
$ kubectl delete deployment myapp-deployment
deployment.apps "myapp-deployment" deleted

$ kubectl create -f deployment-definition.yml --record
Flag --record has been deprecated, --record will be removed in the future
deployment.apps/myapp-deployment created

$ kubectl rollout history deployment.apps/myapp-deployment
deployment.apps/myapp-deployment
REVISION  CHANGE-CAUSE
1         kubectl.exe create --filename=deployment-definition.yml --record=true

```

##### Now let's edit the deployment and then track the recording
##### We will change container image from nginx latest to 1.19
nginx:1.19
```
$ kubectl edit deployment myapp-deployment --record
Flag --record has been deprecated, --record will be removed in the future
deployment.apps/myapp-deployment edited

$ kubectl rollout history deployment.apps/myapp-deployment
deployment.apps/myapp-deployment 
REVISION  CHANGE-CAUSE
1         kubectl.exe create --filename=deployment-definition.yml --record=true
2         kubectl.exe edit deployment myapp-deployment --record=true
```


##### You can see the rolling updates in the event section when you describe the deployment

```
$ kubectl describe deployment myapp-deployment
Name:                   myapp-deployment
Namespace:              default
CreationTimestamp:      Mon, 05 Dec 2022 13:14:48 +0000
Labels:                 app=myapp
Annotations:            deployment.kubernetes.io/revision: 2
                        kubernetes.io/change-cause: kubectl.exe edit deployment myapp-deployment --record=true
Selector:               app=myapps
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=myapps
  Containers:
   nginx-containers:
    Image:        nginx:1.19
    Port:         <none>
    Host Port:    <none>
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   myapp-deployment-66dcb95ffd (3/3 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  121m   deployment-controller  Scaled up replica set myapp-deployment-6769cb8647 to 3
  Normal  ScalingReplicaSet  2m13s  deployment-controller  Scaled up replica set myapp-deployment-66dcb95ffd to 1
  Normal  ScalingReplicaSet  96s    deployment-controller  Scaled down replica set myapp-deployment-6769cb8647 to 2 from 3
  Normal  ScalingReplicaSet  96s    deployment-controller  Scaled up replica set myapp-deployment-66dcb95ffd to 2 from 1
  Normal  ScalingReplicaSet  93s    deployment-controller  Scaled down replica set myapp-deployment-6769cb8647 to 1 from 2
  Normal  ScalingReplicaSet  93s    deployment-controller  Scaled up replica set myapp-deployment-66dcb95ffd to 3 from 2
  Normal  ScalingReplicaSet  89s    deployment-controller  Scaled down replica set myapp-deployment-6769cb8647 to 0 from 1

```


##### Currently let's check our current deployment

```
$ kubectl get deployments
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
myapp-deployment   6/6     6            6           6d2h


$ kubectl get pods
NAME                                READY   STATUS    RESTARTS      AGE
myapp-deployment-66dcb95ffd-7pq9c   1/1     Running   1 (21m ago)   6d
myapp-deployment-66dcb95ffd-dhkhv   1/1     Running   0             26s
myapp-deployment-66dcb95ffd-dnflv   1/1     Running   0             26s
myapp-deployment-66dcb95ffd-n6p2s   1/1     Running   0             26s
myapp-deployment-66dcb95ffd-p7mb2   1/1     Running   1 (21m ago)   6d
myapp-deployment-66dcb95ffd-xm269   1/1     Running   1 (21m ago)   6d
```


##### We have 6 pods running in our minikube single node cluster

##### I will create a new directory called service and within it, a file called service-definition.yml

```
mkdir service | touch service/service-definition.yml
```

```
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  selector:
    app: myapps
  type: NodePort
  
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30004
```

```
$ cd service

$ kubectl create -f service-definition.yml 
service/myapp-service created

$ kubectl get service
NAME            TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes      ClusterIP   10.96.0.1        <none>        443/TCP        16d
myapp-service   NodePort    10.102.126.214   <none>        80:30004/TCP   10m
```
##### Retrieve service url
```
$ minikube service myapp-service --url
```

##### ---------------------------
##### START BUILDING AND DEPLOYING 5 MICRO SERVICES APPLICATION IN KUBERNETES CLUSTER

##### 1. VOTING APP
```
mkdir VOTING-APP | touch VOTING-APP/voting-app-pod.yml
```
##### voting-app-pod.yml

```
apiVersion: v1 
kind: Pod
metadata:
   name: voting-app-pod
   labels:
      name: voting-app-pod
      app: demo-voting-app

spec:
  containers:
    - name: voting-app
      image: kodekloud/examplevotingapp_vote:v1
      ports:
        - containerPort: 80
        

```

```
$ touch VOTING-APP/result-app-pod.yml
```
```
apiVersion: v1 
kind: Pod
metadata:
   name: result-app-pod
   labels:
      name: result-app-pod
      app: demo-voting-app

spec:
  containers:
    - name: result-app
      image: kodekloud/examplevotingapp_result:v1
      ports:
        - containerPort: 80
        

```
$ touch VOTING-APP/redis-pod.yml
```
```
apiVersion: v1 
kind: Pod
metadata:
   name: redis-pod
   labels:
      name: redis-pod
      app: demo-voting-app

spec:
  containers:
    - name: redis
      image: redis
      ports:
        - containerPort: 6379
        

```


$ touch VOTING-APP/postgres-pod.yml
```
```
apiVersion: v1 
kind: Pod
metadata:
   name: postgres-pod
   labels:
      name: postgres-pod
      app: demo-voting-app

spec:
  containers:
    - name: postgres
      image: postgres
      ports:
        - containerPort: 5432
      env:
        - name: POSTGRESS_USER
          value: "postgres"
        - name: POSTGRES_PASSWORD
          value: "postgres"  
        
```


$ touch VOTING-APP/worker-pod.yml
```
```
apiVersion: v1 
kind: Pod
metadata:
   name: worker-pod
   labels:
      name: worker-pod
      app: demo-voting-app

spec:
  containers:
    - name: worker-app
      image: kodekloud/examplevotingapp_worker:v1
      
        

```
##### NEXT I WILL SET UP THE SERVICES. The worker pod doesn't need a service as no pod pr service needs to connect to it.

```
$ touch VOTING-APP/redis-service.yml

$ touch VOTING-APP/postgres-service.yml

$ touch VOTING-APP/voting-app-service.yml                                                                          

$ touch VOTING-APP/result-service.yml
```
##### redis-service.yml
```
apiVersion: v1 
kind: Service
metadata:
   name: redis
   labels:
      name: redis-service
      app: demo-voting-app

spec:
  ports:
    - targetPort: 6379
      port: 6379

  selector:
    name: redis-pod
    app: demo-voting-app

    
```

##### postgres-service.yml
```
apiVersion: v1 
kind: Service
metadata:
   name: db
   labels:
      name: postgres-service
      app: demo-voting-app

spec:
  ports:
    - targetPort: 5432
      port: 5432

  selector:
    name: postgres-pod
    app: demo-voting-app

  ```

  ##### voting-app-service.yml

  ```
apiVersion: v1 
kind: Service
metadata:
   name: voting-service
   labels:
      name: voting-service
      app: demo-voting-app

spec:
  type: NodePort
  ports:
    - targetPort: 80
      port: 80
      nodePort: 30004

  selector:
    name: voting-app-pod
    app: demo-voting-app

  ```

  ##### result-service.yml

  ```
apiVersion: v1 
kind: Service
metadata:
   name: result-service
   labels:
      name: result-service
      app: demo-voting-app

spec:
  type: NodePort
  ports:
    - targetPort: 80
      port: 80
      nodePort: 30005

  selector:
    name: result-app-pod
    app: demo-voting-app



  ```

  ###### Let's start creating the objects

  ```
$ kubectl create -f voting-app-pod.yml 
pod/voting-app-pod created

$ kubectl create -f voting-app-service.yml 
service/voting-service created

$ kubectl get pods,svc
NAME                 READY   STATUS    RESTARTS   AGE
pod/voting-app-pod   1/1     Running   0          6m8s

NAME                     TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE  
service/kubernetes       ClusterIP   10.96.0.1       <none>        443/TCP        17d  
service/voting-service   NodePort    10.109.194.12   <none>        80:30004/TCP   5m23s
  ```
```
$ kubectl create -f redis-pod.yml 
pod/redis-pod created

deles@DESKTOP-PURLK18 MINGW64 ~/Documents/kubernetes-microservices-architecture/VOTING-APP (main)
$ kubectl create -f redis-service.yml 
service/redis created
```
```
$ kubectl get pods,service
NAME                 READY   STATUS    RESTARTS   AGE
pod/redis-pod        1/1     Running   0          71s
pod/voting-app-pod   1/1     Running   0          47m

NAME                     TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
service/kubernetes       ClusterIP   10.96.0.1       <none>        443/TCP        18d
service/redis            ClusterIP   10.100.87.82    <none>        6379/TCP       48s
service/voting-service   NodePort    10.109.194.12   <none>        80:30004/TCP   46m
```