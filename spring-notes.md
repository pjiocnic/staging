https://medium.com/@caio.psw/deploying-and-scalling-your-springboot-application-in-a-kubernetes-cluster-part1-77ad8c6c8d82

Step 1: k8s-deploy.yml
```
apiVersion: apps/v1beta2
kind: Deployment
metadata:
   name: k8s-spring-sample-deploy
spec:
   replicas: 3
   selector:
      matchLabels:
         app: k8s-spring-sample
   minReadySeconds: 10
   strategy:
      type: RollingUpdate
      rollingUpdate:
         maxUnavailable: 1
         maxSurge: 1
   template:
      metadata:
         labels:
            app: k8s-spring-sample
      spec:
         containers:
         - name: k8s-spring-sample
           image: caiopereirasousa/spring-boot-sample
           ports:
           - containerPort: 8080

kubectl apply -f k8s-deploy.yml

kubectl get pods
```

Step 2: k8s-svc.yml
```
apiVersion: v1
kind: Service
metadata:
   name: k8s-spring-sample-service
   labels:
      app: k8s-spring-sample
spec:
   type: NodePort
   ports:
   - port: 8080
     nodePort: 30001
     protocol: TCP
   selector:
      app: k8s-spring-sample
      
kubectl apply -f k8s-svc.yml

http://localhost:30001
```

---

Step 3: svc-k8s-gke.yml
```
apiVersion: v1
kind: Service
metadata:
   name: k8s-spring-sample-service-gke
spec:
   type: LoadBalancer
   ports:
   - port: 80
     targetPort: 8080
   selector:
      app: k8s-spring-sample

kubectl apply -f svc-k8s-gke.yml

```     
git clone https://github.com/caio-ps/k8s-spring-sample.git
      

