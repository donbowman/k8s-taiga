# k8s-taiga

A helm chart to deploy taiga.
Works in conjunction with the taiga-container-build
project (which provides the containers).

It enables Google OAUTH2 login

## Usage

Copy values.yml.sample to values.yml. Fill
in the values (including getting your credentials
from your google [console](https://console.developers.google.com/apis/credentials)

## Persistent Storage

By default this uses the auto-provisioner to create three persistent
volumes (1 for postgresql, 1 for the git repo, 1 for backup).  This means
that if you delete the chart, you will delete the storage. If you
wish, you can manually create one or both volumes, and add them to the
values.yaml as

```
taiga_db_claim: pv-taiga-db-claim
taiga_volume_claim: pv-taiga-volume-claim
taiga_backup_claim: pv-taiga-backup-claim
```

And you may wish to create them as:

```
cat << EOF | kubectl apply -f -
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: retained
provisioner: kubernetes.io/gce-pd
parameters:
  type: pd-ssd
reclaimPolicy: Retain
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pv-taiga-volume-claim
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
  storageClassName: retained
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pv-taiga-backup-claim
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
  storageClassName: retained
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pv-taiga-db-claim
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
  storageClassName: retained
EOF
```



## License

This is all licensed under Apache 2.0
[license](https://www.apache.org/licenses/LICENSE-2.0).
