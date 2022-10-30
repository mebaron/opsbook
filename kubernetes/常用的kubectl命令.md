### 监控相关
#### 获取节点cadvisor指标

```bash
kubectl get --raw=/api/v1/nodes/<node_ip>/proxy/metrics/cadvisor

# 查看有哪些指标名
kubectl get --raw=/api/v1/nodes/<node_ip>/proxy/metrics/cadvisor | grep -v "#" | awk -F '{' '{print $1}' | awk '{print $1}' | sort | uniq

```
#### 获取节点kubelet指标

```bash
kubectl get --raw=/api/v1/nodes/<node_ip>/proxy/metrics

```
#### 获取指定namespace下所有pod指标

```bash
kubectl get --raw "/apis/metrics.k8s.io/v1beta1/namespaces/<namespace_name>/pods/"

```
#### 获取指定pod的指标

```bash
kubectl get --raw "/apis/metrics.k8s.io/v1beta1/namespaces/<namespace_name>/pods/<pod_name>"

```
### Node相关
#### 查看节点内核、podcidr、系统镜像信息、运行时信息

```bash
kubectl get node -ocustom-columns=节点名称:.metadata.name,节点IP:.status.addresses[0].address,节点内核:.status.nodeInfo.kernelVersion,podCIDR:.spec.podCIDR,系统镜像:.status.nodeInfo.osImage,运行时信息:.status.nodeInfo.containerRuntimeVersion

```
#### 查看节点的总可用资源

```bash
kubectl get no -o=custom-columns="NODE:.metadata.name,ALLOCATABLE CPU:.status.allocatable.cpu,ALLOCATABLE MEMORY:.status.allocatable.memory"

```
#### 查看节点已分配资源情况

```bash
kubectl get nodes --no-headers | awk '{print $1}' | xargs -I {} sh -c "echo {} ; kubectl describe node {} | grep Allocated -A 10 | grep -ve Event -ve Allocated -ve percent -ve --;"

```
### Pod相关
#### 强制删除pod

```bash
kubectl delete pod <pod_name> -n <namespace_name> --force --grace-period=0

```
#### 批量清理Evicted的pod

```bash
kubectl get pod -o wide --all-namespaces | awk '{if($4=="Evicted"){cmd="kubectl -n "$1" delete pod "$2; system(cmd)}}'

```

### 其他命令
#### 查看集群webhook

```bash
kubectl get -A ValidatingWebhookConfiguration

kubectl get -A MutatingWebhookConfiguration

```











