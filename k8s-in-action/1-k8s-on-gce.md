https://console.cloud.google.com/kubernetes
https://cloud.google.com/sdk/gcloud/reference/container/clusters/delete

# create 3 node cluster
```
gcloud config set compute/zone us-east4-a
gcloud container clusters create kubia --num-nodes 3 --machine-type f1-micro

pjnie89@cloudshell:~ (smart-inn-237811)$ kubectl get nodes
NAME                                   STATUS   ROLES    AGE     VERSION
gke-kubia-default-pool-fe9eb301-3qm3   Ready    <none>   7m33s   v1.12.7-gke.10
gke-kubia-default-pool-fe9eb301-v82z   Ready    <none>   7m34s   v1.12.7-gke.10
gke-kubia-default-pool-fe9eb301-w8rg   Ready    <none>   7m35s   v1.12.7-gke.10

kubectl describe node gke-kubia-default-pool-fe9eb301-3qm3
```

## Deploying your Node.js app
```
kubectl run kubia --image=luksa/kubia --port=8080 --generator=run/v1
kubectl get pods
kubectl expose rc kubia --type=LoadBalancer --name kubia-http
kubectl get services                                                                                      
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)          AGE
kubernetes   ClusterIP      10.15.240.1     <none>           443/TCP          13m
kubia-http   LoadBalancer   10.15.252.122   35.236.208.115   8080:32511/TCP   54s
http://35.236.208.115:8080
You've hit kubia-9wczr
```
**Question**: can I hit the Cluster-IP@curl 10.15.252.122:8080

# Horizontally scaling the application
```
pjnie89@cloudshell:~ (smart-inn-237811)$ kubectl get replicationcontrollers
NAME    DESIRED   CURRENT   READY   AGE
kubia   1         1         1       5m37s

pjnie89@cloudshell:~ (smart-inn-237811)$ kubectl scale rc kubia --replicas=3
replicationcontroller/kubia scaled

pjnie89@cloudshell:~ (smart-inn-237811)$ kubectl get replicationcontrollers
NAME    DESIRED   CURRENT   READY   AGE
kubia   3         3         1       6m30s


pjnie89@cloudshell:~ (smart-inn-237811)$ kubectl get pods -o wide
NAME          READY   STATUS    RESTARTS   AGE   IP          NODE                                   NOMINATED NODE
kubia-6plnl   1/1     Running   0          29m   10.12.1.4   gke-kubia-default-pool-fe9eb301-v82z   <none>
kubia-9wczr   1/1     Running   0          35m   10.12.0.5   gke-kubia-default-pool-fe9eb301-w8rg   <none>
kubia-skxv5   1/1     Running   0          29m   10.12.2.6   gke-kubia-default-pool-fe9eb301-3qm3   <none>


pjnie89@cloudshell:~ (smart-inn-237811)$ kubectl describe pod kubia-6plnl
Name:               kubia-6plnl
Namespace:          default
Priority:           0
PriorityClassName:  <none>
Node:               gke-kubia-default-pool-fe9eb301-v82z/10.150.0.5
Start Time:         Fri, 24 May 2019 22:39:35 -0400
Labels:             run=kubia
Annotations:        kubernetes.io/limit-ranger: LimitRanger plugin set: cpu request for container kubia
Status:             Running
IP:                 10.12.1.4
Controlled By:      ReplicationController/kubia
Containers:
  kubia:
    Container ID:   docker://bc65c4fa4194d35bfd0a1bad90bf077e7f03a59f59d29959dc758ddca3f3456f
    Image:          luksa/kubia
    Image ID:       docker-pullable://luksa/kubia@sha256:3f28e304dc0f63dc30f273a4202096f0fa0d08510bd2ee7e1032ce600616de24
    Port:           8080/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Fri, 24 May 2019 22:40:10 -0400
    Ready:          True
    Restart Count:  0
    Requests:
      cpu:        100m
    Environment:  <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-77lvm (ro)
```

kubectl cluster-info | grep dashboard
gcloud container clusters describe kubia | grep -E "(username|password):"

## Deleting the cluster
gcloud container clusters delete kubia
