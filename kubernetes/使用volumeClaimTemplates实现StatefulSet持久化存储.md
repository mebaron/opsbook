#### yaml配置如下
```yaml
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  labels:
    k8s-app: tmp-sts
    qcloud-app: tmp-sts
  name: tmp-sts
  namespace: default
spec:
  replicas: 2
  selector:
    matchLabels:
      k8s-app: tmp-sts
      qcloud-app: tmp-sts
  template:
    metadata:
      labels:
        k8s-app: tmp-sts
        qcloud-app: tmp-sts
    spec:
      containers:
      - image: nginx:latest
        imagePullPolicy: Always
        name: nginx
        volumeMounts:
        - mountPath: /data
          name: data
      restartPolicy: Always
  volumeClaimTemplates:
  - apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: data
    spec:
      accessModes:
      - ReadWriteOnce
      resources:
        requests:
          storage: 20Gi
      storageClassName: cbs-ssd
      volumeMode: Filesystem
---
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: cbs-ssd
parameters:
  diskChargeType: POSTPAID_BY_HOUR
  diskType: CLOUD_SSD
provisioner: com.tencent.cloud.csi.cbs
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer

```
