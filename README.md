# ERP-Manifest

## To Run Application:

### First Apply MongoDB StateFulSet and Service
```bash
kubectl apply -f Database/.
```

### Login into `mongod-0` pod to Initiate Replication
```bash
kubectl exec -it mongod-0 -- mongosh
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
# Login into pod
kubectl exec mongod-1 -- /bin/bash

# Login into mongodb with primary preferred
mongosh mongodb://mongod-1.mongodb-service:27017/?readPreference=primaryPreferred

# To get all entries
db.getCollectionNames().map(
    (name) => ({[name]: db[name].find().toArray()})
)
```
#### For `mongod-2`
```bash
# Login into pod
kubectl exec mongod-2 -- /bin/bash

# Login into mongodb with primary preferred
mongosh mongodb://mongod-2.mongodb-service:27017/?readPreference=primaryPreferred

# To get all entries
db.getCollectionNames().map(
    (name) => ({[name]: db[name].find().toArray()})
)
```

## If you want to change primary
### Eg: 
- #### mongod-0 - SECONDARY // want it to be primary
- #### mongod-1 - SECONDARY
- #### mongod-2 - PRIMARY

### For `mongod-1`
```bash
kubectl exec mongod-1 -- mongosh --eval 'rs.freeze(120)'
```
### For `mongod-2`
```bash
kubectl exec mongod-2 -- mongosh --eval 'rs.stepDown(120)'
```
### After above command `mongod-0` will be primary

### Apply Backend
```bash
kubectl apply -f Backend/.
```