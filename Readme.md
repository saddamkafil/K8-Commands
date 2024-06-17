## Get namespaces
```
kubectl get ns
```

## Create namespaces
```
kubectl create ns namespace-name
```

## Edit ns
```
kubectl edit ns namespace_name
```

## Delete ns
```
kubectl delete ns namespace-name
```
### Set default namespace to custome
kubectl config set-context --current --namespace=myns

### 
## Get pods
```
kubectl get pods
```

## Get pods in ns
```
kubectl get pods -n namespace-name
```

## Create pods in namespace
```
kubectl run pod-name --image=image-name -n name-space
```
## Delete pod in NS

### EX
``` 
kubectl delete pod pod-name -n namespace-name
```
```
kubectl delete pod hello-pod -n dev
```

## Print Pod manefestfile
```
kubectl run hello-pod --image=tomcat -n dev --dry-run=client -o yaml
```
### Output: 
``[ec2-user@ip-x-x-x-159 ~]$ kubectl run hello-pod --image=tomcat -n dev --dry-run=client -o yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: hello-pod
  name: hello-pod
  namespace: dev
spec:
  containers:
  - image: tomcat
    name: hello-pod
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
``
## Create yaml file for the pod
```
kubectl run pod-name --image=imagename -n namespace-name --dry-run=client -o yaml > pod.yaml
```
#### EX:
``
ec2-user@ip-x-x-x-159 ~]$ kubectl run hello-pod --image=tomcat -n dev --dry-run=client -o yaml > pod.yaml
[ec2-user@ip-x-x-x-159 ~]$ cat pod.yaml 
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: hello-pod
  name: hello-pod
  namespace: dev
spec:
  containers:
  - image: tomcat
    name: hello-pod
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {} 
``

## Create pod from yaml file
```
kubectl create -f yaml-filename.yaml
```
### Ex
``
$ kubectl create -f pod.yaml
pod/hello-pod created

``

## Delete pod using yaml file
```
kubectl delete -f yamlfile-name.yaml
```

### Ex
``
$ kubectl delete -f pod.yaml
pod "hello-pod" deleted
``

## Create Deployment 
kubectl create deployment deploy1 --image=tomcat  -n dev

$ kubectl create deployment deploy1 --image=tomcat  -n dev
deployment.apps/deploy1 created

## Get Deployemnt (verification)
$ kubectl get deployment,pod -n dev
NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/deploy1   1/1     1            1           19s

NAME                           READY   STATUS    RESTARTS   AGE
pod/deploy1-675df89578-l2cfr   1/1     Running   0          19s

## Print Yaml for deployment 

```
$ kubectl create deployment deploy1 --image=tomcat  -n dev --dry-run=client -o yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: deploy1
  name: deploy1
  namespace: dev
spec:
  replicas: 1
  selector:
    matchLabels:
      app: deploy1
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: deploy1
    spec:
      containers:
      - image: tomcat
        name: tomcat
        resources: {}
status: {}
``

## Export/Create Deployment Yaml file
```
$ kubectl create deployment deploy1 --image=tomcat  -n dev --dry-run=client -o yaml > deployment.yaml
```

### Print Deployment YAML in  JSON
```
kubectl create deployment deploy1 --image=tomcat  -n dev --dry-run=client -o json

{
    "kind": "Deployment",
    "apiVersion": "apps/v1",
    "metadata": {
        "name": "deploy1",
        "namespace": "dev",
        "creationTimestamp": null,
        "labels": {
            "app": "deploy1"
        }
    },
    "spec": {
        "replicas": 1,
        "selector": {
            "matchLabels": {
                "app": "deploy1"
            }
        },
        "template": {
            "metadata": {
                "creationTimestamp": null,
                "labels": {
                    "app": "deploy1"
                }
            },
            "spec": {
                "containers": [
                    {
                        "name": "tomcat",
                        "image": "tomcat",
                        "resources": {}
                    }
                ]
            }
        },
        "strategy": {}
    },
    "status": {}
}
```

### create deployment from yaml-file
kk create deployment dep-name --image=image-name -n name-sapce --dry-run=client -o yaml > deploymnet-yaml.yaml
Example: 
kk create deployment dep5 --image=busybox --port=80 -n dev --dry-run=client -o yaml > dep5.yaml

### Apply change to deployment 
kk apply -f deployment.yaml

Ex: kk apply -f dep5.yaml

## Create Replicas / Scale deployment 
``
$ kubectl scale deployment deploy1 --replicas=2 -n dev
deployment.apps/deploy1 scaled
``

## Verify Deployment / Scale 

``
[ec2-user@ip-x-x-x-159 ~]$ kubectl get deployment -n dev
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
deploy1   1/1     1            1           7m45s
[ec2-user@ip-x-x-x-159 ~]$ kubectl scale deployment deploy1 --replicas=2 -n dev
deployment.apps/deploy1 scaled
[ec2-user@ip-x-x-x-159 ~]$ kubectl get deployment -n dev
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
deploy1   2/2     2            2           9m23s
``

## Delete deployment 
```
Kubectl delete deployment deplyment-name -n namespace-name
```
### EX
``
$ kubectl delete deployment deploy1 -n dev
deployment.apps "deploy1" deleted
``


## Delete Deployment from yaml file

``
$ kubectl delete -f deployment.yaml
deployment.apps "deploy1" deleted
``
## Describe Deployment 

``
kubectl describe deployment deployment-name -n dev
``
#### Output:
``
$ kubectl describe deployment deployment-name -n dev
Name:                   deployment-name
Namespace:              dev
CreationTimestamp:      Sun, 19 Jun 2022 03:06:54 +0000
Labels:                 app=deployment-name
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=deployment-name
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=deployment-name
  Containers:
   nginx:
    Image:        nginx:1.14.2
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
NewReplicaSet:   deployment-name-b75984b59 (1/1 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  47s   deployment-controller  Scaled up replica set deployment-name-b75984b59 to 1



## Change Image version / Image in deployment 
```
kubectl set image deployment/deployment-name nginx=nginx:1.16.1 -n dev
```
### Output: 
``
$ kubectl set image deployment/deployment-name nginx=nginx:1.16.1 -n dev
deployment.apps/deployment-name image updated
``

## Check Deployment status after changing image

```
kubectl rollout status deployment/deployment-name -n dev
```
### Output:
``
$ $ kubectl rollout status deployment/deployment-name -n dev
deployment "deployment-name" successfully rolled out
``

## Rollout history for Deployment 

``
$ kubectl rollout status deployment/deployment-name -n dev
deployment "deployment-name" successfully rolled out
``

## Checck Revision of Deployment 
``
[ec2-user@ip-x-x-x-159 ~]$ kubectl rollout history deployment/deployment-name -n dev --revision=2
deployment.apps/deployment-name with revision #2
Pod Template:
  Labels:	app=deployment-name
	pod-template-hash=6c67cd4f68
  Containers:
   nginx:
    Image:	nginx:1.16.1
    Port:	<none>
    Host Port:	<none>
    Environment:	<none>
    Mounts:	<none>
  Volumes:	<none>

[ec2-user@ip-x-x-x-159 ~]$ kubectl rollout history deployment/deployment-name -n dev --revision=1
deployment.apps/deployment-name with revision #1
Pod Template:
  Labels:	app=deployment-name
	pod-template-hash=b75984b59
  Containers:
   nginx:
    Image:	nginx:1.14.2
    Port:	<none>
    Host Port:	<none>
    Environment:	<none>
    Mounts:	<none>
  Volumes:	<none>


``

## Rollback to previos verion/revision number

``
$ kubectl rollout undo  deployment/deployment-name -n dev --to-revision=1
deployment.apps/deployment-name rolled back
``

## HPA

```
kubectl get deployment -n dev
```
 ## CReate hpa for deployment 

 ``` 
 kubectl autoscale deployment/deployment-name --min=3 --max=10 --cpu-percent=80 -n dev
 ```
 ### Output: $ kubectl autoscale deployment/deployment-name --min=3 --max=10 --cpu-percent=80 -n dev
horizontalpodautoscaler.autoscaling/deployment-name autoscaled

### create SVC
kubectl expose deployment dep --port=8080 --type=LoadBalancer -n dev

### describe SVC
kubectl describe svc dep -n dev

### check logs of pod 
kk logs -f dep1-5c9699ff4b-cblbv -n dev

#### Secrets ######################################################

## Create Secrets yaml file for secret type Volume #####
kubectl create secret generic secret-name --from-literal==username=username --from-literal=password=1234 -n namespacename --dry-run=client -o yaml > secretfile.yaml
Ex: kk create secret generic sec1  --from-literal=username=saddam --from-literal=password=1234 -n dev --dry-run=client -o yaml > sec1.yaml

### How to decretpt secret
echo "secretvalue" | base64 -d

## Apply secrets to deployment 
ex: kk apply -f dep1-sec1.yaml

### Create yaml file for variable secret type


### Create docker registry secrets
kk create secret docker-registry sec-docker-repo --docker-server=https://index.docker.io/v1/
 --docker-username=dockerusername --docker-password=dockerhub-password --docker-email=email@gmail.com -n dev --dry-run=client -o yaml > sec-docker.yaml 

## Create yaml file using get command
kk get sa default -o yaml > sa.yaml

## Replace existing deployment with replace command
kk replace sa default -f sa.yaml

## Lable Node 
kubectl label <NODE_NAME> short_name=worker1

############### Storage,PV,PVC ########



