### HPA - Horizontal Pod Scaling on K8
###### HPA allows your application to scale Up/Down on demand. HPA automatically scales the number of pods in a replication controller, deployment or replica set. HPA is the performace toolkit for K8. The below guide aims to test and understand HPA on CPU utilization. 

##### STEP#1: Setup Metric server: </br>
  - git clone https://github.com/kubernetes-incubator/metrics-server.git </br>
  - Navigate till **metrics-server/deploy/1.8+/** </br>
  - Introduce the below section in **metrics-server-deployment.yaml** </br> </br>

```
…
…
…

name: metrics-server
        image: k8s.gcr.io/metrics-server-amd64:v0.3.5
        command:
          - /metrics-server
          - --kubelet-insecure-tls
        imagePullPolicy: Always
        volumeMounts:
…
…
…
        
 ```
##### STEP#2: Start the Metric Server: </br>
  - Navigate till **metrics-server/deploy/1.8+/** </br>
  - Issue the below command: </br>
  ```
  kubectl apply -f .
  ```
  
##### STEP#3: Check if the Metric Server is Up and Running: </br> 
```
 kubectl -n kube-system get pods
```
##### STEP#4: Make a Deployment:
```
vi hpadeployment.yml
```
```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: appdeploymentfromyml
  labels:
    app: appdeploymentfromyml
spec:
  replicas: 1
  selector:
    matchLabels:
      app: appdeploymentfromyml
  template:
    metadata:
      labels:
        app: appdeploymentfromyml
    spec:
      containers:
      - name: appdeploymentfromyml
        image: rahulvaish/springbootdocker
        ports:
        - containerPort: 8080
          protocol: TCP
        resources:
          limits:
            cpu: 100m
          requests:
            cpu: 100m
            
```
```
kubectl apply -f hpadeployment.yml
```
##### STEP#6: Expose the Deployment as a Service:

```
vi hpaservice.yml
```
```
apiVersion: v1
kind: Service
metadata:
  labels:
    app: appservicefromyml
  name: appservicefromyml
  namespace: default
spec:
  type: NodePort
  ports:
  - nodePort: 31746
    port: 8080
    protocol: TCP
    name: http
    targetPort: 8080
  selector:
    app: appdeploymentfromyml

```
```
kubectl apply -f hpaservice.yml
```
##### STEP#7: Create Autoscaling YML:
```
vi hpa.yml
```

```
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: apphpafromyml
spec:
  maxReplicas: 5
  minReplicas: 1
  scaleTargetRef:
    apiVersion: extensions/v1beta1
    kind: Deployment
    name: appdeploymentfromyml
  targetCPUUtilizationPercentage: 20
  
```
```
kubectl apply -f hpa.yml
```
##### STEP#8: Test using *siege* utility:
siege -q -c 10 -t 2m http://localhost:31746/

![image](https://user-images.githubusercontent.com/689226/65833314-931fba00-e2ec-11e9-8d29-9a5f1a572ffc.png)

<hr>

