# apiVersion

Says it's complicated; wonder if there is a different version for each API.

* Core items just use **v1**
* Replicatsets and deployments use **apps/v1**

```
kubectl get all
```

---

## APPLY

You can use kubectl apply to create or update resources. However, to update a resource you should have created the resource by using kubectl apply or kubectl create --save-config. For more information about using kubectl apply to update resources, see 

```
kubectl apply -f first-pod.yaml
```

Apply all config files:

```
kubectl apply -f .
```

---

## EXECUTE Command in POD

Examples:

```
kubectl exec webapp ls
kubectl -it exec webapp sh
```

---

## Deployment

Deployments create replicasets.

View display deployment history.

```
kubectl rollout history deploy webapp
```

Check deployment progress.

```
kubectl rollout status deploy webapp:
```

Rollback to previous version:

```
kubectl rollout undo deploy webapp
```

Rollback to a specific rollout version:

```
kubectl rollout undo deploy webapp --to-revision=2
```

---

## Deployment Issues

**ImagePullBackOff** Will try to pull the image in an infinite loop.

---

## Networking

If the services were deployed into a single POD, bot containers can see each other using localhost.

+------------- POD --------------+
|   APP        <->       DB      |
+--------------------------------+

If the POD fails, was it the app or the DB. For the most part it's better to have them in seperate PODS.

Each POD is also allocated a **Service**.  The IP addresses for the services are dynamic.

Kubenetes maintains it's own private DNS service **kube-dns**. Each POD can access other services using the service name e.g. **webapp**.

Finding a service in a different namespace e.g.:

```
database.mynamespace
```

---

## Namespaces

A way or partitioning resources into different areas. In an application is 1000's of PODs and Services.

What are the different ways to partition name-spaces e.g. front-end / back-end.

If you don't specifiy a namespace, everything goes into the **default** namespaces.

When you need information on a POD, Service etc in a namespace you need to refer to that namespace e.g.

```
kubectl describe svc kube-dns -n kube-system
```

---

# Persistance

**PV**  = Persistent Volumes and **PVC** = Persistent Volumes Claims

Made of two (2) parts:

1. volumeMounts
1. persistentVolumeClaim

Using **persistentVolumeClaim** in the workload files allows us to configure how the data is stored seperately from the application structure.

1. [storageClassName](https://kubernetes.io/docs/concepts/storage/storage-classes/)
1. [spec.{type-of-volume}](https://kubernetes.io/docs/concepts/storage/volumes/#types-of-volumes)

---

## Persistance - accessModes

The access modes are:

* ReadWriteOnce – the volume can be mounted as read-write by a single node
* ReadOnlyMany – the volume can be mounted read-only by many nodes
* ReadWriteMany – the volume can be mounted as read-write by many nodes

---

## AWS PersistantVolume

[AWS docs create-volume](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-volume.html) - The size of the volume, in GiBs.

```
aws ec2 create-volume --availability-zone=eu-west-1a --size=20 --volume-type=gp2
```

Configuration would look something like:

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: local-storage
spec:
  storageClassName: awsstorage
  capacity:
    storage: 20Gi
  accessModes:
    - ReadWriteOnce
  awsElasticBlockStore:
      volumeID: <volume-id>
      fsType: ext4
```
