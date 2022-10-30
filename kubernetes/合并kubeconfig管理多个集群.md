### 1、使用kubectl命令合并
#### 1.1 查看环境配置
```bash
# 测试kubeconfig文件如下
ls
cls1.conf  cls.conf

# kubeconfig文件路径
pwd
/root/test

```
#### 1.2 使用kubectl命令合并
```bash
# 什么kubeconfig环境变量指向测试kubeconfig文件
export KUBECONFIG=$KUBECONFIG:$HOME/test/cls.conf:$HOME/test/cls1.conf

# 合并
kubectl config view --flatten > tmp.conf


```
**注意**：使用命令合并的时候，如`kubeconfig`配置中的`user`字段一样，则证书信息会覆盖，导致合并后的文件不符合预期，需要适当调整`user`字段的值之后合并使用。

### 2、使用kubecm工具合并
#### 2.1 kubecm工具介绍
[kubecm](https://github.com/sunny0826/kubecm)是一款专门管理杂乱无章的`kubeconfig`的小工具。[工具下载地址](https://github.com/sunny0826/kubecm/releases)bebe内网使用版本为：v0.21.0。`kubecm`工具命令一览：
```bash
kubecm


        Manage your kubeconfig more easily.


██   ██ ██    ██ ██████  ███████  ██████ ███    ███
██  ██  ██    ██ ██   ██ ██      ██      ████  ████
█████   ██    ██ ██████  █████   ██      ██ ████ ██
██  ██  ██    ██ ██   ██ ██      ██      ██  ██  ██
██   ██  ██████  ██████  ███████  ██████ ██      ██

 Tips  Find more information at: kubecm.cloud (https://kubecm.cloud)

Usage:
  kubecm [command]

Available Commands:
  add         Add KubeConfig to $HOME/.kube/config
  alias       Generate alias for all contexts
  clear       Clear lapsed context, cluster and user
  cloud       Manage kubeconfig from cloud
  completion  Generate completion script
  create      Create new KubeConfig(experiment)
  delete      Delete the specified context from the kubeconfig
  help        Help about any command
  list        List KubeConfig
  merge       Merge multiple kubeconfig files into one
  namespace   Switch or change namespace interactively
  rename      Rename the contexts of kubeconfig
  switch      Switch Kube Context interactively
  version     Print version info

```
#### 2.2 查看环境配置
```bash
# 测试kubeconfig文件如下
ls
cls1.conf  cls.conf

# kubeconfig文件路径
pwd
/root/test

```
#### 2.3 使用kubecm合并
```bash

# 和原生命令不同的是，kubecm可直接合并，如遇到相同的user，工具会自动调整。
kubecm merge -f /root/test --config tmp.conf

```
