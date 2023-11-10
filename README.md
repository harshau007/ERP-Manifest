# ERP-Manifest

## To Run Application:

### First Apply MongoDB StateFulSet and Service
```bash
kubectl apply -f Database/.
```

### Login into `mongod-0` pod to Initiate Replication
```bash
kubectl exec -it mongod-0 -- mongo
```

### Run Following Command to Initiate Replication
```bash
rs.initiate(
{
    _id: "MainRepSet",
    members: [
    { _id: 0, host: "mongod-0.mongodb-service.default.svc.cluster.local:27017" },
    { _id: 1, host: "mongod-1.mongodb-service.default.svc.cluster.local:27017" },
    { _id: 2, host: "mongod-2.mongodb-service.default.svc.cluster.local:27017" }
  ]
})
```

### Run following command `mongod-1` and `mongod-2` pods to set as replicas

#### For `mongod-1`
```bash
kubectl exec mongod-1 -- sh -c "echo 'rs.secondaryOk()' > ~/.mongorc.js"
```
#### For `mongod-2`
```bash
kubectl exec mongod-2 -- sh -c "echo 'rs.secondaryOk()' > ~/.mongorc.js"
```

### Apply Backend
```bash
kubectl apply -f Backend/.
```