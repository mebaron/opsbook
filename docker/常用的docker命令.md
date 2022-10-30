
### 清理磁盘，删除关闭的容器、无用的数据卷和网络，以及dangling镜像
```bash
docker system prune -a
```

### 获取pod中容器的id
```bash
# 仅适用单容器pod，待优化
# containerd运行时
kubectl describe pod <pod_name> -n <namespace_name> | grep -A10 "^Containers:" | grep -Eo 'containerd://.*$' | head -n 1 | sed 's/containerd:\/\/\(.*\)$/\1/'

# docker运行时
kubectl describe pod <pod_name> -n <namespace_name> | grep -A10 "^Containers:" | grep -Eo 'docker://.*$' | head -n 1 | sed 's/docker:\/\/\(.*\)$/\1/'
```

### 通过容器id拿到容器对应进程pid
```bash
docker inspect -f {{.State.Pid}} <容器id>

```

### 查找占用镜像层的容器id
```bash
docker ps | awk 'NR>1' | awk '{print $1}' | xargs -n 1 -I {} -- sh -c "echo {} && docker inspect {} |grep <镜像层layer id> -C 1"

```