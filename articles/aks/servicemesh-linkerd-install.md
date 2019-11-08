---
title: 在 Azure Kubernetes Service （AKS）中安装 Linkerd
description: 了解如何安装并使用 Linkerd 在 Azure Kubernetes 服务（AKS）群集中创建服务网格
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: f70052a62152a20f808c1e491a663d1406fbd407
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747710"
---
# <a name="install-linkerd-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service （AKS）中安装 Linkerd

[Linkerd][linkerd-github]是开源服务网格和[CNCF 孵化项目][linkerd-cncf]。 Linkerd 是一种 ultralight 服务网格，提供的功能包括流量管理、服务标识和安全性、可靠性和可观察性。 有关 Linkerd 的详细信息，请参阅官方[LINKERD FAQ][linkerd-faq]和[Linkerd 体系结构][linkerd-architecture]文档。

本文介绍如何安装 Linkerd。 Linkerd `linkerd` 客户端二进制文件安装在客户端计算机上，Linkerd 组件安装在 AKS 上的 Kubernetes 群集。

> [!NOTE]
> 这些说明引用 Linkerd 版本 `stable-2.6.0`。
>
> 可针对 Kubernetes 版本 `1.13+`运行 Linkerd `stable-2.6.x`。 你可以在[GitHub-Linkerd 版本][linkerd-github-releases]中找到其他稳定的 Linkerd 版本。

在本文中，学习如何：

> [!div class="checklist"]
> * 下载并安装 Linkerd Linkerd 客户端二进制文件
> * 在 AKS 上安装 Linkerd
> * 验证 Linkerd 安装
> * 访问仪表板
> * 从 AKS 卸载 Linkerd

## <a name="before-you-begin"></a>开始之前

本文中详述的步骤假设已创建 AKS 群集（已启用 RBAC 的 Kubernetes `1.13` 及更高版本）并已与该群集建立 `kubectl` 连接。 如果需要帮助完成这些项目，请参阅 [AKS 快速入门][aks-quickstart]。

必须将所有 Linkerd pod 计划为在 Linux 节点上运行-此设置是下面详细说明的默认安装方法，不需要其他配置。

本文将 Linkerd 安装指南分为几个不同的步骤。 结果与官方 Linkerd 入门[指南][linkerd-getting-started]的结构相同。

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/linkerd/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/linkerd/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/linkerd/install-client-binary-windows.md)]

::: zone-end

## <a name="install-linkerd-on-aks"></a>在 AKS 上安装 Linkerd

安装 Linkerd 之前，我们将运行预安装检查以确定是否可以在我们的 AKS 群集上安装控制平面：

```console
linkerd check --pre
```

应会看到如下所示的内容，指示 AKS 群集是 Linkerd 的有效安装目标：

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

pre-kubernetes-setup
--------------------
√ control plane namespace does not already exist
√ can create Namespaces
√ can create ClusterRoles
√ can create ClusterRoleBindings
√ can create CustomResourceDefinitions
√ can create PodSecurityPolicies
√ can create ServiceAccounts
√ can create Services
√ can create Deployments
√ can create CronJobs
√ can create ConfigMaps
√ no clock skew detected

pre-kubernetes-capability
-------------------------
√ has NET_ADMIN capability
√ has NET_RAW capability

pre-linkerd-global-resources
----------------------------
√ no ClusterRoles exist
√ no ClusterRoleBindings exist
√ no CustomResourceDefinitions exist
√ no MutatingWebhookConfigurations exist
√ no ValidatingWebhookConfigurations exist
√ no PodSecurityPolicies exist

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

Status check results are √
```

现在可以安装 Linkerd 组件了。 使用 `linkerd` 和 `kubectl` 二进制文件将 Linkerd 组件安装到 AKS 群集中。 将自动创建 `linkerd` 命名空间，组件将安装到此命名空间中。

```console
linkerd install | kubectl apply -f -
```

Linkerd 部署多个对象。 你将在上面的 `linkerd install` 命令的输出中看到列表。 部署 Linkerd 组件需要大约1分钟的时间才能完成，具体取决于群集环境。

此时，已将 Linkerd 部署到 AKS 群集。 若要确保成功部署 Linkerd，请转到下一节，[验证 Linkerd 安装](#validate-the-linkerd-installation)。

## <a name="validate-the-linkerd-installation"></a>验证 Linkerd 安装

确认已成功创建资源。 使用[kubectl get svc][kubectl-get]和[kubectl get pod][kubectl-get]命令来查询 `linkerd` 命名空间，该命名空间由 `linkerd install` 命令安装 Linkerd 组件：

```console
kubectl get svc --namespace linkerd --output wide
kubectl get pod --namespace linkerd --output wide
```

以下示例输出显示现在应正在运行的服务和 pod （在 Linux 节点上计划）：

```console
NAME                     TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)             AGE  SELECTOR
linkerd-controller-api   ClusterIP   10.0.110.67    <none>        8085/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-destination      ClusterIP   10.0.224.29    <none>        8086/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-dst              ClusterIP   10.0.225.148   <none>        8086/TCP            66s  linkerd.io/control-plane-component=destination
linkerd-grafana          ClusterIP   10.0.61.124    <none>        3000/TCP            65s  linkerd.io/control-plane-component=grafana
linkerd-identity         ClusterIP   10.0.6.104     <none>        8080/TCP            67s  linkerd.io/control-plane-component=identity
linkerd-prometheus       ClusterIP   10.0.27.168    <none>        9090/TCP            65s  linkerd.io/control-plane-component=prometheus
linkerd-proxy-injector   ClusterIP   10.0.100.133   <none>        443/TCP             64s  linkerd.io/control-plane-component=proxy-injector
linkerd-sp-validator     ClusterIP   10.0.221.5     <none>        443/TCP             64s  linkerd.io/control-plane-component=sp-validator
linkerd-tap              ClusterIP   10.0.18.14     <none>        8088/TCP,443/TCP    64s  linkerd.io/control-plane-component=tap
linkerd-web              ClusterIP   10.0.37.108    <none>        8084/TCP,9994/TCP   66s  linkerd.io/control-plane-component=web

NAME                                      READY   STATUS    RESTARTS   AGE   IP            NODE                            NOMINATED NODE   READINESS GATES
linkerd-controller-66ddc9f94f-cm9kt       3/3     Running   0          66s   10.240.0.50   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-destination-c94bc454-qpkng        2/2     Running   0          66s   10.240.0.78   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-grafana-6868fdcb66-4cmq2          2/2     Running   0          65s   10.240.0.69   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-identity-74d8df4b85-tqq8f         2/2     Running   0          66s   10.240.0.48   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-prometheus-699587cf8-k8ghg        2/2     Running   0          65s   10.240.0.41   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-proxy-injector-6556447f64-n29wr   2/2     Running   0          64s   10.240.0.32   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-sp-validator-56745cd567-v4x7h     2/2     Running   0          64s   10.240.0.6    aks-linux-16165125-vmss000000   <none>           <none>
linkerd-tap-5cd9fc566-ct988               2/2     Running   0          64s   10.240.0.15   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-web-774c79b6d5-dhhwf              2/2     Running   0          65s   10.240.0.70   aks-linux-16165125-vmss000002   <none>           <none>
```

Linkerd 通过 `linkerd` 的客户端二进制文件提供了一个命令，用于验证是否已成功安装并配置了 Linkerd 控制平面。

```console
linkerd check
```

你应看到类似于下面的内容，指示你的安装已成功：

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

linkerd-config
--------------
√ control plane Namespace exists
√ control plane ClusterRoles exist
√ control plane ClusterRoleBindings exist
√ control plane ServiceAccounts exist
√ control plane CustomResourceDefinitions exist
√ control plane MutatingWebhookConfigurations exist
√ control plane ValidatingWebhookConfigurations exist
√ control plane PodSecurityPolicies exist

linkerd-existence
-----------------
√ 'linkerd-config' config map exists
√ heartbeat ServiceAccount exist
√ control plane replica sets are ready
√ no unschedulable pods
√ controller pod is running
√ can initialize the client
√ can query the control plane API

linkerd-api
-----------
√ control plane pods are ready
√ control plane self-check
√ [kubernetes] control plane can talk to Kubernetes
√ [prometheus] control plane can talk to Prometheus
√ no invalid service profiles

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

control-plane-version
---------------------
√ control plane is up-to-date
√ control plane and cli versions match

Status check results are √
```

## <a name="access-the-dashboard"></a>访问仪表板

Linkerd 附带了一个仪表板，可用于深入了解服务网格和工作负荷。 若要访问仪表板，请使用 `linkerd dashboard` 命令。 此命令利用[kubectl 端口转发][kubectl-port-forward]，在你的客户端计算机与 AKS 群集中的相关 pod 之间创建安全连接。 然后，它会自动在默认浏览器中打开仪表板。

```console
linkerd dashboard
```

此命令还会创建端口转发，并返回 Grafana 仪表板的链接。

```console
Linkerd dashboard available at:
http://127.0.0.1:50750
Grafana dashboard available at:
http://127.0.0.1:50750/grafana
Opening Linkerd dashboard in the default browser
```

## <a name="uninstall-linkerd-from-aks"></a>从 AKS 卸载 Linkerd

> [!WARNING]
> 删除正在运行的系统中的 Linkerd 可能会导致服务之间出现与流量相关的问题。 在继续操作之前，请确保您的系统的预配仍可正常运行，而无需 Linkerd。

首先需要删除数据平面代理。 从工作负荷命名空间中删除任何自动代理注入[批注][linkerd-automatic-proxy-injection]，并汇总工作负荷部署。 工作负荷将不再具有任何关联的数据平面组件。

最后，按如下所示删除控制平面：

```console
linkerd install --ignore-cluster | kubectl delete -f -
```

## <a name="next-steps"></a>后续步骤

若要了解有关 Linkerd 的更多安装和配置选项，请参阅以下官方 Linkerd 指南：

- [Linkerd-Helm 安装][linkerd-install-with-helm]
- [Linkerd-针对角色权限的多阶段安装][linkerd-multi-stage-installation]

你还可以使用以下方案：

- [Linkerd emojivoto 演示][linkerd-demo-emojivoto]
- [Linkerd 书籍演示][linkerd-demo-books]

<!-- LINKS - external -->

[linkerd]: https://linkerd.io/
[linkerd-cncf]: https://landscape.cncf.io/selected=linkerd
[linkerd-faq]: https://linkerd.io/2/faq/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-getting-started]: https://linkerd.io/2/getting-started/
[linkerd-overview]: https://linkerd.io/2/overview/
[linkerd-github]: https://github.com/linkerd/linkerd2
[linkerd-github-releases]: https://github.com/linkerd/linkerd2/releases/

[linkerd-install-with-helm]: https://linkerd.io/2/tasks/install-helm/
[linkerd-multi-stage-installation]: https://linkerd.io/2/tasks/install/#multi-stage-install
[linkerd-automatic-proxy-injection]: https://linkerd.io/2/features/proxy-injection/

[linkerd-demo-emojivoto]: https://linkerd.io/2/getting-started/#step-5-install-the-demo-app
[linkerd-demo-books]: https://linkerd.io/2/tasks/books/

[helm]: https://helm.sh

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
