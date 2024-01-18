# Summary

* [简介](README.md)

## linux运维笔记
* [【linux】shell脚本模板](linux/shell-script-template.md)
* [【linux】腾讯云cvm上搭建openvpn](linux/cvm-build-openvpn.md)
* [【linux】vmware安装centos环境设置静态的ip地址](linux/vmware-install-centos-set-static-ip-address.md)
* [【linux】使用awk处理字段信息](linux/Use-awk-to-extract-field-information.md)
* [【linux】Linux系统atop监控工具的安装和使用](linux/Installation-and-use-of-atop-monitoring-tool-in-Linux-system.md)
* [【linux】iptables基础知识](linux/Basic-knowledge-of-iptables.md)

## 数据库运维笔记
* [【datebase】mysql运维笔记](database/mysql-devops-note.md)
* [【datebase】mongodb运维笔记](database/mongodb-devops-note.md)
* [【datebase】redis运维笔记](database/redis-devops-note.md)
* [【datebase】elasticsearch运维笔记](database/elasticsearch-devops-note.md)

## docker/containerd运维笔记
* [【docker】dockerfile学习笔记](docker/dockerfile-study.md)
* [【docker】docker-compose学习笔记](docker/docker-compose-study.md)
* [【containerd】containerd的安装和使用](docker/Installation-and-use-of-containerd.md)
* [【docker】docker限制容器可占用的磁盘空间](docker/Docker-limits-the-disk-space-that-containers-can-occupy.md)
* [【docker】buildkit入门使用](docker/Getting-started-with-buildkit.md)

## kubernetes运维笔记
* [【k8s】k8s常用命令总结](k8s/k8s-common-commands.md)
* [【k8s】强制删除Terminating状态ns](k8s/k8s-force-delete-terminating-ns.md)
* [【k8s】k8s中filebeat作为sidecar采集容器日志](k8s/userd-filebeat-as-sidecar-collect-log.md)
* [【k8s】k8s中生成自定义用户kubeconfig](k8s/k8s-generate-kubeonfig.md)
* [【k8s】kubecm管理多k8s集群](k8s/kubecm-manages-k8s-clusters.md)
* [【k8s】kustomize入门实践](k8s/getting-started-with-kustomize-actually.md)
* [【k8s】用脚本对pod进行健康探测](k8s/Use-scripts-to-perform-health-detection-on-pods.md)
* [【k8s】批量删除集群内terminating状态的ns](k8s/Batch-delete-ns-in-terminating-state-in-the-cluster.md)
* [【k8s】批量修改命名空间下容器的resources配置](k8s/Modify-the-resources-configuration-of-the-container-under-the-namespace.md)
* [【k8s】alpine镜像无法用telnet访问redis](k8s/Alpine-mirror-cannot-access-redis-with-telnet.md)
* [【k8s】coredns服务pod启动报错listen tcp :8080: bind: address already in use](k8s/coredns-startup-error-8080-port-address-already-in-use.md)
* [【k8s】docker logs和kubectl logs查看日志不一致](k8s/Docker-logs-and-kubectl-logs-view-logs-are-inconsistent.md)
* [【k8s】k8s部署hostport模式pod报错端口冲突](k8s/k8s-deploys-hostport-mode-pod-error-report.md)
* [【k8s】k8s通过endpoint引入外部服务访问异常](k8s/k8s-introduces-external-service-access-exception-through-endpoint.md)
* [【k8s】pod挂载nfs数据卷报错权限不足](k8s/pod-mounting-nfs-data-volume-error-report-insufficient-permissions.md)
* [【k8s】service绑定后端endpoint异常](k8s/Service-binding-backend-endpoint-exception.md)
* [【k8s】StatefulSet类型pod域名无法解析](k8s/StatefulSet-type-pod-domain-name-cannot-be-resolved.md)
* [【k8s】k8s集群内pod内无法正确解析coredns中的hosts配置](k8s/The-hosts-configuration-in-coredns-cannot-be-correctly-parsed-in-pods-in-the-k8s-cluster.md)
* [【k8s】k8s中配置prestop不生效](k8s/The-prestop-configuration-in-k8s-does-not-take-effect.md)

## TKE运维笔记
* [【tke】tke上使用docker-in-docker](tke/tke-used-docker-in-docker.md)
* [【tke】tke上部署Jumpserver跳板机](tke/tke-deploy-jumpserver.md)
* [【tke】获取pvc对应的cbs-id](tke/get-the-cbsId-of-pvc-in-the-tke-cluster.md)
* [【tke】清除集群中所有的evited状态pod](tke/clean-the-evicted-state-pod-in-the-cluster.md)
* [【tke】调整命名空间所有deploy和sts的副本数](tke/adjust-workload-replicas-under-the-namespace.md)
* [【tke】如何在非root用户启动的镜像中设置挂载目录权限](tke/modify-the-permissions-of-the-container-mount-directory.md)
* [【tke】alpine镜像内解析域名超5s](tke/The-domain-name-resolved-in-the-alpine-container-exceeds-5s.md)
* [【tke】TKE重新部署cert-manager一直卡主](tke/TKE-redeploys-cert-manager-and-keeps-getting-stuck.md)
* [【tke】无法通过负载均衡类型ingress访问后端服务](tke/Cannot-access-back-end-services-through-load-balancing-type-ingress.md)
* [【tke】pod启动报错mount destination xxxx not absolute](tke/Pod-startup-error-report-mount-destination-xxxx-not-absolute.md)
* [【tke】pod采用组件挂载cfs报错](tke/Pod-uses-components-to-mount-cfs-to-report-errors.md)
* [【tke】TKE集群内pod无法解析service name](tke/The-pod-in-the-tke-cluster-cannot-resolve-the-service-name.md)
* [【tke】TKE集群内pod挂载cos/cfs类型pvc超时](tke/tke-cluster-pod-mount-cos-or-cfs-pvc-timeout.md)

## prometheus运维笔记
* [【prometheus】prometheus入门知识概念](prometheus/basic-knowledge-of-prometheus.md)
* [【prometheus】Prometheus采集Docker Engine Metrics](prometheus/Prometheus-collects-Docker-Engine-Metrics.md)
* [【prometheus】使用Blackbox_exporter服务探测](prometheus/Blackbox_exporter-monitoring-url.md)
* [【prometheus】prometheus监控k8s集群coredns](prometheus/prometheus-monitors-k8s-cluster-coredns.md)
* [【prometheus】prometheus常用告警promsql](prometheus/Prometheus-common-alarm-promsql.md)

## istio运维笔记
* [【istio】istio注解列表](istio/istio-annotation-list.md)
* [【istio】istio中sidecar启动顺序问题](istio/Sidecar-startup-sequence-problem-in-istio.md)
* [【istio】istio流量转移实践](istio/istio-traffic-shift.md)
* [【istio】istio多集群就近接入](istio/istio-multi-cluster-nearby-access.md)
* [【istio】VirtualService中hosts字段的配置使用](istio/VirtualService-host-configuration-and-use.md)
* [【istio】gateway配置http强转https](istio/Gateway-configuration-http-forced-to-https.md)
* [【istio】istio sidecar注入](istio/istio-sidecar-injection.md)
* [【istio】如何修改istio的sidecar资源配置](istio/How-to-modify-the-sidecar-resource-configuration.md)
* [【istio】pod注入sidecar后无法连接mysql](istio/Cannot-connect-to-mysql-after-pod-injects-sidecar.md)

## 面试问题
* [【面试】面试之redis篇](interview/redis.md)
* [【面试】面试之go篇](interview/go.md)

