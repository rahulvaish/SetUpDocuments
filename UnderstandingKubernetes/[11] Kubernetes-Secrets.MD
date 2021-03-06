### Secrets in K8
###### Secrets are mechanism in Kubernetes, to store protected data. The below guide aims to setup and understand secrets in Kubernetes.

##### STEP#1: Get a password for your plain text: 

```
echo -n "my-password" | base64
```
##### STEP#2:  Prepare a YML to create secret

```
vi secret.yml
```

    apiVersion: v1
    kind: Secret
    metadata:
      name: my-secret
    type: Opaque
    data:
      ROOT_PASSWORD: bXktcGFzc3dvcmQ=


```
kubectl apply -f secret.yml
```

##### STEP#3: Refer [this example](https://github.com/rahulvaish/ReferenceDocuments/blob/master/UnderstandingKubernetes/%5B13%5D%20Kubernetes-MySQL.MD) to understand how secrets can be associated with deployments.
