---
title: 在 Azure Kubernetes Service （AKS）中安装 Consul
description: 了解如何安装并使用 Consul 在 Azure Kubernetes 服务（AKS）群集中创建服务网格
author: dstrebel
ms.topic: article
ms.date: 10/09/2019
ms.author: dastrebe
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 06ca2327b2859ffb0f5b314d7b92082d5a83dc48
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77594227"
---
# <a name="install-and-use-consul-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务（AKS）中安装并使用 Consul

[Consul][consul-github]是一种开放源代码服务网格，提供跨 Kubernetes 群集中微服务的一组关键功能。 这些功能包括服务发现、运行状况检查、服务分段和可观察性。 有关 Consul 的详细信息，请参阅官方[Is Consul？][consul-docs-concepts]文档。

本文介绍如何安装 Consul。 Consul 组件安装在 AKS 上的 Kubernetes 群集中。

> [!NOTE]
> 这些说明引用 Consul 版本 `1.6.0`，并使用至少 Helm 版本 `2.14.2`。
>
> 可针对 Kubernetes 版本 `1.13+`运行 Consul `1.6.x` 版本。 可在[GitHub-Consul 版本][consul-github-releases]中找到其他 Consul 版本，并在[Consul-发行说明][consul-release-notes]中找到有关每个版本的信息。

在本文中，学习如何：

> [!div class="checklist"]
> * 在 AKS 上安装 Consul 组件
> * 验证 Consul 安装
> * 从 AKS 卸载 Consul

## <a name="before-you-begin"></a>开始之前

本文详细介绍了如何创建 AKS 群集（Kubernetes `1.13` 及更高版本，启用 RBAC），并已与群集建立了 `kubectl` 连接。 如果需要有关这些项的帮助，请参阅[AKS 快速入门][aks-quickstart]。 确保群集至少有3个节点。

需要[Helm][helm]来遵循这些说明，并安装 Consul。 建议在群集中正确安装并配置最新的稳定版本。 如果需要有关安装 Helm 的帮助，请参阅[AKS Helm 安装指南][helm-install]。 还必须计划在 Linux 节点上运行所有 Consul。

本文将 Consul 安装指南分为几个不同的步骤。 最终结果在结构上与正式 Consul 安装[指南][consul-install-k8]相同。

### <a name="install-the-consul-components-on-aks"></a>在 AKS 上安装 Consul 组件

首先，下载 Consul Helm 图表 `v0.10.0` 版本。 此版本的图表包括 Consul 版本 `1.6.0`。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download](includes/servicemesh/consul/download-powershell.md)]

::: zone-end

使用 Helm 和下载的 `consul-helm` 图将 Consul 组件安装到 AKS 群集的 `consul` 命名空间中。 

> [!NOTE]
> **安装选项**
> 
> 我们将在安装过程中使用以下选项：
> - `connectInject.enabled=true`-启用要注入到 pod 中的代理
> - `client.enabled=true`-启用要在每个节点上运行的 Consul 客户端
> - `client.grpc=true`-启用 connectInject 的 gRPC 侦听器
> - `syncCatalog.enabled=true`-sync Kubernetes 和 Consul services
>
> **节点选择器**
>
> Consul 目前必须计划在 Linux 节点上运行。 如果群集中有 Windows Server 节点，则必须确保 Consul pod 仅计划在 Linux 节点上运行。 我们将使用[节点选择器][kubernetes-node-selectors]来确保将 pod 安排到正确的节点。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/consul/install-components-powershell.md)]

::: zone-end

`Consul` Helm 图表会部署多个对象。 你可以从上述 `helm install` 命令的输出中查看列表。 部署 Consul 组件可能需要大约3分钟的时间才能完成，具体取决于群集环境。

此时，已将 Consul 部署到 AKS 群集。 若要确保已成功部署 Consul，请转到下一节，验证 Consul 安装。

## <a name="validate-the-consul-installation"></a>验证 Consul 安装

确认已成功创建资源。 使用[kubectl get svc][kubectl-get]和[kubectl get pod][kubectl-get]命令来查询 `consul` 命名空间，该命名空间由 `helm install` 命令安装 Consul 组件：

```console
kubectl get svc --namespace consul --output wide
kubectl get pod --namespace consul --output wide
```

以下示例输出显示现在应正在运行的服务和 pod （在 Linux 节点上计划）：

```console
NAME                                 TYPE           CLUSTER-IP    EXTERNAL-IP             PORT(S)                                                                   AGE     SELECTOR
consul                               ExternalName   <none>        consul.service.consul   <none>                                                                    38s     <none>
consul-consul-connect-injector-svc   ClusterIP      10.0.98.102   <none>                  443/TCP                                                                   3m26s   app=consul,component=connect-injector,release=consul
consul-consul-dns                    ClusterIP      10.0.46.194   <none>                  53/TCP,53/UDP                                                             3m26s   app=consul,hasDNS=true,release=consul
consul-consul-server                 ClusterIP      None          <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   3m26s   app=consul,component=server,release=consul
consul-consul-ui                     ClusterIP      10.0.50.188   <none>                  80/TCP                                                                    3m26s   app=consul,component=server,release=consul

NAME                                                              READY   STATUS    RESTARTS   AGE    IP            NODE                            NOMINATED NODE   READINESS GATES
consul-consul-connect-injector-webhook-deployment-99f74fdbcr5zj   1/1     Running   0          3m9s   10.240.0.68   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-jbksc                                               1/1     Running   0          3m9s   10.240.0.44   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-jkwtq                                               1/1     Running   0          3m9s   10.240.0.70   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-0                                            1/1     Running   0          3m9s   10.240.0.91   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-1                                            1/1     Running   0          3m9s   10.240.0.38   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-server-2                                            1/1     Running   0          3m9s   10.240.0.10   aks-linux-92468653-vmss000000   <none>           <none>
consul-consul-sync-catalog-d846b79c-8ssr8                         1/1     Running   2          3m9s   10.240.0.94   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-tz2t5                                               1/1     Running   0          3m9s   10.240.0.12   aks-linux-92468653-vmss000000   <none>           <none>
```

所有 pod 都应显示 `Running`状态。 如果 Pod 没有这些状态，请在运行之前等待一两分钟。 如果任何 pod 报告了问题，请使用[kubectl 说明 pod][kubectl-describe]命令查看其输出和状态。

## <a name="accessing-the-consul-ui"></a>访问 Consul UI

在上面的设置中安装 Consul UI，并提供基于 UI 的 Consul 配置。 不通过外部 ip 地址公开 Consul 的 UI。 若要访问 Consul 用户界面，请使用[kubectl 端口转发][kubectl-port-forward]命令。 此命令在你的客户端计算机与你的 AKS 群集中的相关 pod 之间创建安全连接。

```azurecli
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```

你现在可以打开浏览器并使其指向 `http://localhost:8080/ui` 打开 Consul UI。 打开 UI 时，应会看到以下内容：

![Consul UI](./media/servicemesh/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>从 AKS 卸载 Consul

> [!WARNING]
> 删除正在运行的系统中的 Consul 可能会导致服务之间出现与流量相关的问题。 在继续操作之前，请确保您的系统的预配仍可正常运行，而无需 Consul。

### <a name="remove-consul-components-and-namespace"></a>删除 Consul 组件和命名空间

若要从 AKS 群集中删除 Consul，请使用以下命令。 `helm delete` 命令将删除 `consul` 图表，`kubectl delete namespace` 命令将删除 `consul` 命名空间。

```azurecli
helm delete --purge consul
kubectl delete namespace consul
```

## <a name="next-steps"></a>后续步骤

若要了解 Consul 的更多安装和配置选项，请参阅以下官方 Consul 文章：

- [Consul-Helm 安装指南][consul-install-k8]
- [Consul-Helm 安装选项][consul-install-helm-options]

你还可以使用以下方案：

- [Consul 示例应用程序][consul-app-example]

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
[kubernetes-node-selectors]: https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#node-selectors

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md
