---
title: 使用 Helm 在 Kubernetes on Azure 中部署容器
description: 使用 Helm 打包工具在 Azure Kubernetes 服务 (AKS) 群集中部署容器
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/13/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: dd2deba25615373765dd3492d03c1ba547c8ba8c
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2018
ms.locfileid: "39055128"
---
# <a name="install-applications-with-helm-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 中使用 Helm 安装应用程序

[Helm][helm] 是一种开放源打包工具，有助于安装和管理 Kubernetes 应用程序的生命周期。 与诸如 *APT* 和 *Yum* 的 Linux 包管理器类似，Helm 用于管理 Kubernetes 图表，这些图表是预配置的 Kubernetes 资源包。

本文介绍如何在 AKS 上的 Kubernetes 群集中配置和使用 Helm。

## <a name="before-you-begin"></a>开始之前

本文档详述的步骤假设你已创建 AKS 群集并已与该群集建立 `kubectl` 连接。 如果需要这些项，请参阅 [AKS 快速入门][aks-quickstart]。

## <a name="install-helm-cli"></a>安装 Helm CLI

Helm CLI 是一个在开发系统上运行的客户端，可让你使用 Helm 启动、停止和管理应用程序。

如果使用 Azure Cloud Shell，则已安装 Helm CLI。 若要在 Mac 上安装 Helm CLI，请使用 `brew`。 有关更多安装选项，请参阅[安装 Helm][helm-install-options]。

```console
brew install kubernetes-helm
```

输出：

```
==> Downloading https://homebrew.bintray.com/bottles/kubernetes-helm-2.9.1.high_sierra.bottle.tar.gz
######################################################################## 100.0%
==> Pouring kubernetes-helm-2.9.1.high_sierra.bottle.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/kubernetes-helm/2.9.1: 50 files, 66.2MB
```

## <a name="create-a-service-account"></a>创建服务帐户

在启用 RBAC 的群集中部署 Helm 之前，需要 Tiller 服务的服务帐户和角色绑定。 有关在启用 RBAC 的群集中保护 Helm/Tiller 的详细信息，请参阅 [Tiller、命名空间和 RBAC][tiller-rbac]。 如果群集未启用 RBAC，请跳过此步骤。

创建名为 `helm-rbac.yaml` 的文件，并将其复制到以下 YAML 中：

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

使用 `kubectl create` 命令创建服务帐户和角色绑定：

```console
kubectl create -f helm-rbac.yaml
```

## <a name="secure-tiller-and-helm"></a>保护 Tiller 和 Helm

Helm 客户端和 Tiller 服务使用 TLS/SSL 进行身份验证和相互通信。 此身份验证方法有助于保护 Kubernetes 群集以及可以部署的服务。 为了提高安全性，可以生成自己的签名证书。 每个 Helm 用户都会收到自己的客户端证书，并且 Tiller 将在 Kubernetes 群集中初始化并应用证书。 有关详细信息，请参阅[在 Helm 和 Tiller 之间使用 TLS/SSL][helm-ssl]。

使用启用 RBAC 的 Kubernetes 群集，可以控制 Tiller 对群集的访问级别。 可以定义部署 Tiller 的 Kubernetes 命名空间，并限制 Tiller 可以在其中部署资源的命名空间。 此方法允许你在不同的命名空间中创建 Tiller 实例并限制部署边界，并将 Helm 客户端的用户范围限定为某些命名空间。 有关详细信息，请参阅 [Helm 基于角色的访问控制][helm-rbac]。

## <a name="configure-helm"></a>配置 Helm

若要将基本 Tiller 部署到 AKS 群集，请使用 [helm init][helm-init] 命令。 如果群集未启用 RBAC，请删除 `--service-account` 参数和值。 如果已为 Tiller 和 Helm 配置了 TLS/SSL，请跳过此基本初始化步骤，而是提供所需的 `--tiller-tls-`，如下一个示例所示。

```console
helm init --service-account tiller
```

如果已在 Helm 和 Tiller 之间配置了 TLS/SSL，则提供 `--tiller-tls-` 参数和自己证书的名称，如以下示例所示：

```console
helm init \
    --tiller-tls \
    --tiller-tls-cert tiller.cert.pem \
    --tiller-tls-key tiller.key.pem \
    --tiller-tls-verify \
    --tls-ca-cert ca.cert.pem \
    --service-account tiller
```

## <a name="find-helm-charts"></a>查找 Helm 图表

Helm 图表用来将应用程序部署到 Kubernetes 群集中。 若要搜索预创建的 Helm 图表，请使用 [helm search][helm-search] 命令：

```console
helm search
```

以下精简示例输出显示了一些可供使用的 Helm 图表：

```
$ helm search

NAME                           CHART VERSION    APP VERSION  DESCRIPTION
stable/acs-engine-autoscaler   2.2.0            2.1.1        Scales worker nodes within agent pools
stable/aerospike               0.1.7            v3.14.1.2    A Helm chart for Aerospike in Kubernetes
stable/anchore-engine          0.1.7            0.1.10       Anchore container analysis and policy evaluatio...
stable/apm-server              0.1.0            6.2.4        The server receives data from the Elastic APM a...
stable/ark                     1.0.1            0.8.2        A Helm chart for ark
stable/artifactory             7.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/artifactory-ha          0.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/auditbeat               0.1.0            6.2.4        A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler  0.3.3                         Scales worker nodes within autoscaling groups.
stable/bitcoind                0.1.3            0.15.1       Bitcoin is an innovative payment network and a ...
stable/buildkite               0.2.3            3            Agent for Buildkite
stable/burrow                  0.4.4            0.17.1       Burrow is a permissionable smart contract machine
stable/centrifugo              2.0.1            1.7.3        Centrifugo is a real-time messaging server.
stable/cerebro                 0.1.0            0.7.3        A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager            v0.3.3           v0.3.1       A Helm chart for cert-manager
stable/chaoskube               0.7.0            0.8.0        Chaoskube periodically kills random pods in you...
stable/chartmuseum             1.5.0            0.7.0        Helm Chart Repository with support for Amazon S...
stable/chronograf              0.4.5            1.3          Open-source web application written in Go and R...
stable/cluster-autoscaler      0.6.4            1.2.2        Scales worker nodes within autoscaling groups.
stable/cockroachdb             1.1.1            2.0.0        CockroachDB is a scalable, survivable, strongly...
stable/concourse               1.10.1           3.14.1       Concourse is a simple and scalable CI system.
stable/consul                  3.2.0            1.0.0        Highly available and distributed service discov...
stable/coredns                 0.9.0            1.0.6        CoreDNS is a DNS server that chains plugins and...
stable/coscale                 0.2.1            3.9.1        CoScale Agent
stable/dask                    1.0.4            0.17.4       Distributed computation in Python with task sch...
stable/dask-distributed        2.0.2                         DEPRECATED: Distributed computation in Python
stable/datadog                 0.18.0           6.3.0        DataDog Agent
...
```

若要更新图表列表，请使用 [helm repo update][helm-repo-update] 命令。 以下示例显示了成功的存储库更新：

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="run-helm-charts"></a>运行 Helm 图表

若要使用 Helm 安装图表，请使用 [helm install][helm-install] 命令并指定要安装的图表的名称。 若要查看运行中的此命令，让我们使用 Helm 图表安装基本的 Wordpress 部署。 如果配置了 TLS/SSL，请添加 `--tls` 参数以使用 Helm 客户端证书。

```console
helm install stable/wordpress
```

以下精简示例输出显示了 Helm 图表创建的 Kubernetes 资源的部署状态：

```
$ helm install stable/wordpress

NAME:   wishful-mastiff
LAST DEPLOYED: Thu Jul 12 15:53:56 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1beta1/Deployment
NAME                       DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
wishful-mastiff-wordpress  1        1        1           0          1s

==> v1beta1/StatefulSet
NAME                     DESIRED  CURRENT  AGE
wishful-mastiff-mariadb  1        1        1s

==> v1/Pod(related)
NAME                                        READY  STATUS   RESTARTS  AGE
wishful-mastiff-wordpress-6f96f8fdf9-q84sz  0/1    Pending  0         1s
wishful-mastiff-mariadb-0                   0/1    Pending  0         1s

==> v1/Secret
NAME                       TYPE    DATA  AGE
wishful-mastiff-mariadb    Opaque  2     2s
wishful-mastiff-wordpress  Opaque  2     2s

==> v1/ConfigMap
NAME                           DATA  AGE
wishful-mastiff-mariadb        1     2s
wishful-mastiff-mariadb-tests  1     2s

==> v1/PersistentVolumeClaim
NAME                       STATUS   VOLUME   CAPACITY  ACCESS MODES  STORAGECLASS  AGE
wishful-mastiff-wordpress  Pending  default  2s

==> v1/Service
NAME                       TYPE          CLUSTER-IP   EXTERNAL-IP  PORT(S)                     AGE
wishful-mastiff-mariadb    ClusterIP     10.1.116.54  <none>       3306/TCP                    2s
wishful-mastiff-wordpress  LoadBalancer  10.1.217.64  <pending>    80:31751/TCP,443:31264/TCP  2s
...
```

需要一两分钟才能填充 Wordpress 服务的 *EXTERNAL-IP* 地址，并允许你使用 Web 浏览器访问该地址。

## <a name="list-helm-releases"></a>列出 Helm 版本

若要查看群集上已安装的版本列表，请使用 [helm list][helm-list] 命令。 以下示例显示了上一步中部署的 Wordpress 版本。 如果配置了 TLS/SSL，请添加 `--tls` 参数以使用 Helm 客户端证书。

```console
$ helm list

NAME             REVISION    UPDATED                     STATUS      CHART              NAMESPACE
wishful-mastiff  1           Thu Jul 12 15:53:56 2018    DEPLOYED    wordpress-2.1.3  default
```

## <a name="next-steps"></a>后续步骤

有关使用 Helm 管理 Kubernetes 应用程序部署的详细信息，请参阅 Helm 文档。

> [!div class="nextstepaction"]
> [Helm 文档][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-documentation]: https://docs.helm.sh/
[helm-init]: https://docs.helm.sh/helm/#helm-init
[helm-install]: https://docs.helm.sh/helm/#helm-install
[helm-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm-list]: https://docs.helm.sh/helm/#helm-list
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[helm-repo-update]: https://docs.helm.sh/helm/#helm-repo-update
[helm-search]: https://docs.helm.sh/helm/#helm-search
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac
[helm-ssl]: https://docs.helm.sh/using_helm/#using-ssl-between-helm-and-tiller

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
