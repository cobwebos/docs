---
title: 在 Azure Kubernetes 服务 (AKS) 中安装 Hashicorp Consul
description: 了解如何安装并使用 Consul 在 Azure Kubernetes 服务 (AKS) 群集中创建服务网格
services: container-service
author: dstrebel
ms.service: container-service
ms.topic: article
ms.date: 06/19/2019
ms.author: dastrebe
ms.openlocfilehash: 7acd2533079499091427c7e63741b9c587ee29e5
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70189219"
---
# <a name="install-and-use-consul-connect-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 中安装并使用 Consul Connect

[Consul][consul-github]是一种开放源代码服务网格, 提供跨 Kubernetes 群集中微服务的一组关键功能。 这些功能包括服务发现、运行状况检查、服务分段和可观察性。 有关 Consul 的详细信息, 请参阅官方[Is Consul？][consul-docs-concepts]文档。

本文介绍如何安装 Consul。 Consul 组件安装在 AKS 上的 Kubernetes 群集中。

> [!NOTE]
> 这些说明引用 Consul 版本`1.5`。
>
> Hashicorp 团队`1.5.x`针对 Kubernetes 版本`1.12`( `1.14`、) `1.14`对 Consul 版本进行了测试。 可在[GitHub-Consul 版本][consul-github-releases]中找到其他 Consul 版本, 并在[Consul-发行说明][consul-release-notes]中找到有关每个版本的信息。

在本文中，学习如何：

> [!div class="checklist"]
> * 在 AKS 上安装 Consul 组件
> * 验证 Consul 安装
> * 从 AKS 卸载 Consul

## <a name="before-you-begin"></a>开始之前

本文中详述的步骤假设已创建 AKS 群集（已启用 RBAC 的 Kubernetes `1.11` 及更高版本）并已与该群集建立 `kubectl` 连接。 如果需要帮助完成这些项目，请参阅 [AKS 快速入门][aks-quickstart]。

需要[Helm][helm]来遵循这些说明, 并安装 Consul。 建议在群集中正确安装并配置版本 `2.12.2` 或更高版本。 安装 Helm 时如需帮助，请参阅 [AKS Helm 安装指南][helm-install]。 还必须计划在 Linux 节点上运行所有 Consul。

本文将 Consul 安装指南分为几个不同的步骤。 最终结果在结构上与正式 Consul 安装[指南][consul-install-k8]相同。

### <a name="install-the-consul-components-on-aks"></a>在 AKS 上安装 Consul 组件

我们将首先克隆 Kubernetes GitHub 存储库上的官方 HashiCorp Consul。

```bash
git clone https://github.com/hashicorp/consul-helm.git
cd consul-helm
```

接下来, 我们需要创建用于 Consul 安装的自定义 Helm 值文件。 在安装 Consul 之前, 请创建以下自定义值文件。

```bash
vim consul-values.yaml
```

然后, 将以下值复制并粘贴到 consul. yaml 文件中。

```yaml
# Sets datacenter name and version Consul to use
global:
  datacenter: dc1
  image: "consul:1.5.2"

# Enables proxies to be injected into pods
connectInject:
  enabled: true

# Enables UI on ClusterIP
ui:
  service:
    type: "ClusterIP"

# Enables GRCP that is required for connectInject
client:
  enabled: true
  grpc: true

# Sets replicase to 3 for HA
server:
  replicas: 3
  bootstrapExpect: 1
  disruptionBudget:
    enabled: true
    maxUnavailable: 1

# Syncs Kubernetes service discovery with Consul
syncCatalog:
  enabled: true
```

现在, 我们有了自定义值文件, 接下来我们可以将 Consul 安装到 AKS 群集中。

Bash

```bash
helm install -f consul-values.yaml --name consul --namespace consul .
```
`Consul` Helm 图表会部署多个对象。 上述 `helm install` 命令的输出会显示对象列表。 部署 Consul 组件可能需要4到5分钟的时间, 具体取决于群集环境。

> [!NOTE]
> 必须将所有 Consul pod 计划为在 Linux 节点上运行。 如果你有 Windows Server 节点池以及群集上的 Linux 节点池, 请验证是否已将所有 Consul pod 计划在 Linux 节点上运行。

此时, 已将 Consul 部署到 AKS 群集。 若要确保已成功部署 Consul, 请转到下一节, 验证 Consul 安装。

## <a name="validate-the-consul-installation"></a>验证 Consul 安装

首先，确认已创建所需的服务。 使用 [kubectl get svc][kubectl-get] 命令查看正在运行的服务。 查询命名空间, 其中 Consul 组件`consul`由 Helm 图表安装: `consul`

```console
kubectl get svc --namespace consul
```

以下示例输出显示了现在应正在运行的服务：

```console
NAME                                 TYPE           CLUSTER-IP     EXTERNAL-IP             PORT(S)                                                                   AGE
consul                               ExternalName   <none>         consul.service.consul   <none>                                                                    38m
consul-consul-connect-injector-svc   ClusterIP      10.0.89.113    <none>                  443/TCP                                                                   40m
consul-consul-dns                    ClusterIP      10.0.166.82    <none>                  53/TCP,53/UDP                                                             40m
consul-consul-server                 ClusterIP      None           <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   40m
consul-consul-ui                     ClusterIP      10.0.117.164   <none>                  80/TCP                                                                    40m
```

接下来，确认已创建所需的 Pod。 使用 [kubectl get pods][kubectl-get] 命令，然后再次查询 `consul` 命名空间：

```console
kubectl get pods --namespace consul
```

以下示例输出显示了正在运行的 Pod：

```console
NAME                                                              READY   STATUS    RESTARTS   AGE
consul-consul-7cc9v                                               1/1     Running   0          37m
consul-consul-7klg7                                               1/1     Running   0          37m
consul-consul-connect-injector-webhook-deployment-57f88df8hgmfs   1/1     Running   0          37m
consul-consul-lq8qb                                               1/1     Running   0          37m
consul-consul-server-0                                            1/1     Running   0          37m
consul-consul-server-1                                            1/1     Running   0          36m
consul-consul-server-2                                            1/1     Running   0          36m
consul-consul-sync-catalog-7cf7d5bfff-jjbjv                       1/1     Running   2          37m
```

 所有盒应显示状态`Running`。 如果 Pod 没有这些状态，请在运行之前等待一两分钟。 如果任何 Pod 报告问题，请使用 [kubectl describe pod][kubectl-describe] 命令查看其输出和状态。

## <a name="accessing-the-consul-ui"></a>访问 Consul UI

在上面的设置中安装了 Consul UI, 提供基于 UI 的 Consul 配置。 不通过外部 ip 地址公开 Consul 的 UI。 若要访问加载项用户界面，请使用 [kubectl port-forward][kubectl-port-forward] 命令。 此命令在客户端计算机与 AKS 群集中相关 Pod 之间建立安全连接。

```bash
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```
你现在可以打开浏览器并将其`http://localhost:8080/ui`指向, 以打开 Consul UI。 打开 UI 时, 应会看到以下内容:

![Consul UI](./media/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>从 AKS 卸载 Consul

> [!WARNING]
> 删除正在运行的系统中的 Consul 可能会导致服务之间出现与流量相关的问题。 在继续操作之前, 请确保您的系统的预配仍可正常运行, 而无需 Consul。

### <a name="remove-consul-components-and-namespace"></a>删除 Consul 组件和命名空间

若要从 AKS 群集中删除 Consul, 请使用以下命令。 这些`helm delete`命令将删除该`consul`图表, 并且该`kubectl delete ns`命令将删除该`consul`命名空间。

```bash
helm delete --purge consul
kubectl delete ns consul
```

## <a name="next-steps"></a>后续步骤

若要了解 Consul 的更多安装和配置选项, 请参阅以下官方 Consul 文章:

- [Consul-Helm 安装指南][consul-install-k8]
- [Consul-Helm 安装选项][consul-install-helm-options]

还可以使用[Consul 示例应用程序][consul-app-example]执行其他方案。

<!-- LINKS - external -->
[Hashicorp]: https://hashicorp.com
[cosul-github]: https://github.com/hashicorp/consul
[helm]: https://helm.sh

[consul-docs-concepts]: https://www.consul.io/docs/platform/k8s/index.html
[consul-github]: https://github.com/hashicorp/consul
[consul-github-releases]: https://github.com/hashicorp/consul/releases
[consul-release-notes]: https://github.com/hashicorp/consul/blob/master/CHANGELOG.md
[consul-install-download]: https://www.consul.io/downloads.html
[consul-install-k8]: https://www.consul.io/docs/platform/k8s/run.html
[consul-install-helm-options]: https://www.consul.io/docs/platform/k8s/helm.html#configuration-values-
[consul-app-example]: https://github.com/hashicorp/demo-consul-101/tree/master/k8s
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md
