---
title: 限制 Azure Kubernetes Service (AKS) 中的出口流量
description: 了解控制 Azure Kubernetes Service (AKS) 中的出口流量所需的端口和地址
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/06/2019
ms.author: mlearned
ms.openlocfilehash: cf9dc304efea8874d16953f74bf88a4317760819
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69031837"
---
# <a name="preview---limit-egress-traffic-for-cluster-nodes-and-control-access-to-required-ports-and-services-in-azure-kubernetes-service-aks"></a>预览-限制群集节点的出口流量并控制对 Azure Kubernetes Service (AKS) 中所需的端口和服务的访问

默认情况下, AKS 群集具有不受限制的出站 (传出) internet 访问权限。 此级别的网络访问权限允许运行的节点和服务根据需要访问外部资源。 如果希望限制传出流量, 则必须能够访问有限数量的端口和地址才能维护正常的群集维护任务。 然后, 将群集配置为仅使用 Microsoft 容器注册表 (MCR) 或 Azure 容器注册表 (ACR) 中的基本系统容器映像, 而不是外部公共存储库。 你必须配置首选防火墙和安全规则, 以允许这些必需的端口和地址。

本文详细介绍了需要哪些网络端口和完全限定的域名 (Fqdn), 并且如果在 AKS 群集中限制出站流量, 则可以选择此选项。  此功能目前处于预览状态。

> [!IMPORTANT]
> AKS 预览功能是可选的自助服务。 预览按 "原样" 提供, 并从服务级别协议和有限担保中排除。 AKS 预览版是以最大努力为基础的客户支持部分覆盖的。 因此, 这些功能并不用于生产。 有关其他信息, 请参阅以下支持文章:
>
> * [AKS 支持策略][aks-support-policies]
> * [Azure 支持常见问题][aks-faq]

## <a name="before-you-begin"></a>开始之前

需要安装并配置 Azure CLI 版本2.0.66 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

若要创建可限制出站流量的 AKS 群集, 请先在订阅上启用一个功能标志。 此功能注册会将你创建的所有 AKS 群集配置为使用 MCR 或 ACR 中的基本系统容器映像。 若要注册*AKSLockingDownEgressPreview*功能标志, 请使用[az feature register][az-feature-register]命令, 如以下示例中所示:

> [!CAUTION]
> 在订阅上注册功能时, 当前无法注册该功能。 启用某些预览功能后, 默认值可用于在订阅中创建的所有 AKS 群集。 不要对生产订阅启用预览功能。 使用单独的订阅来测试预览功能并收集反馈。

```azurecli-interactive
az feature register --name AKSLockingDownEgressPreview --namespace Microsoft.ContainerService
```

状态显示为“已注册”需要几分钟时间。 您可以使用[az feature list][az-feature-list]命令检查注册状态:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSLockingDownEgressPreview')].{Name:name,State:properties.state}"
```

准备就绪后, 请使用[az provider register][az-provider-register]命令刷新*ContainerService*资源提供程序的注册:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="egress-traffic-overview"></a>出口流量概述

出于管理和操作目的, AKS 群集中的节点需要访问特定端口和完全限定的域名 (Fqdn)。 这些操作可以是与 API 服务器通信, 或下载并安装 core Kubernetes 群集组件和节点安全更新。 默认情况下, AKS 群集中的节点不会限制传出 (出站) internet 流量。 群集可能从外部存储库中提取基本系统容器映像。

若要提高 AKS 群集的安全性, 您可能希望限制出站流量。 群集配置为从 MCR 或 ACR 请求基本系统容器映像。 如果以这种方式锁定出口流量, 则必须定义特定端口和 Fqdn, 以允许 AKS 节点与所需的外部服务正确通信。 如果没有这些授权端口和 Fqdn, 则 AKS 节点无法与 API 服务器通信或安装核心组件。

可以使用[Azure 防火墙][azure-firewall]或第三方防火墙设备来保护出口流量, 并定义这些所需的端口和地址。 AKS 不会自动为你创建这些规则。 当你在网络防火墙中创建适当的规则时, 以下端口和地址用于引用。

在 AKS 中, 有两组端口和地址:

* [AKS 群集所需的端口和地址](#required-ports-and-addresses-for-aks-clusters)详细说明了授权出口流量的最低要求。
* 所有方案都不需要[可选的建议地址和端口用于 AKS 群集](#optional-recommended-addresses-and-ports-for-aks-clusters), 但与其他服务 (如 Azure Monitor) 的集成将不能正常工作。 查看此可选端口和 Fqdn 列表, 并授权 AKS 群集中使用的任何服务和组件。

> [!NOTE]
> 限制传出流量仅适用于启用功能标志注册后创建的新 AKS 群集。 对于现有群集, 请使用`az aks upgrade`命令[执行群集升级操作][aks-upgrade], 然后再限制传出流量。

## <a name="required-ports-and-addresses-for-aks-clusters"></a>AKS 群集所需的端口和地址

AKS 群集需要以下出站端口/网络规则:

* TCP 端口*443*
* TCP 端口*9000*和 tcp 端口*22* (用于隧道前端盒) 与 API 服务器上的隧道通信。
    * 若要获得更具体的信息, 请参阅 * *. hcp。\<location\>. azmk8s.io*和 * *. 运行。\<下\>* 表中的 azmk8s.io 地址。

需要以下 FQDN/应用程序规则:

| FQDN                       | Port      | 使用      |
|----------------------------|-----------|----------|
| *.hcp.\<location\>.azmk8s.io | HTTPS: 443, TCP:22, TCP: 9000 | 此地址为 API 服务器终结点。 将 *\<location\>* 替换为部署 AKS 群集的区域。 |
| *.tun.\<location\>.azmk8s.io | HTTPS: 443, TCP:22, TCP: 9000 | 此地址为 API 服务器终结点。 将 *\<location\>* 替换为部署 AKS 群集的区域。 |
| aksrepos.azurecr.io        | HTTPS:443 | 访问 Azure 容器注册表 (ACR) 中的映像需要此地址。 |
| \* .blob.core.windows.net    | HTTPS:443 | 此地址为 ACR 中存储的图像的后端存储。 |
| mcr.microsoft.com          | HTTPS:443 | 访问 Microsoft 容器注册表 (MCR) 中的图像需要此地址。 |
| *.cdn.mscr.io              | HTTPS:443 | Azure 内容分发网络 (CDN) 支持的 MCR 存储需要此地址。 |
| management.azure.com       | HTTPS:443 | 此地址是 Kubernetes GET/PUT 操作所必需的。 |
| login.microsoftonline.com  | HTTPS:443 | 此地址是 Azure Active Directory 身份验证所必需的。 |
| api.snapcraft.io           | HTTPS: 443, HTTP:80 | 在 Linux 节点上安装管理包需要此地址。 |
| ntp.ubuntu.com             | UDP: 123   | 对于 Linux 节点上的 NTP 时间同步, 此地址是必需的。 |
| *. docker.io                | HTTPS:443 | 需要此地址才能拉取隧道前面的必需容器映像。 |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>适用于 AKS 群集的可选推荐地址和端口

* 适用于 DNS 的 UDP 端口*53*

为了使 AKS 群集正常运行, 建议使用以下 FQDN/应用程序规则:

| FQDN                                    | Port      | 使用      |
|-----------------------------------------|-----------|----------|
| *.ubuntu.com                            | HTTP:80   | 此地址允许 Linux 群集节点下载所需的安全修补程序和更新。 |
| packages.microsoft.com                  | HTTPS:443 | 此地址是用于缓存*apt*操作的 Microsoft 包存储库。 |
| dc.services.visualstudio.com            | HTTPS:443 | 建议使用 Azure Monitor 进行正确的指标和监视。 |
| *.opinsights.azure.com                  | HTTPS:443 | 建议使用 Azure Monitor 进行正确的指标和监视。 |
| *.monitoring.azure.com                  | HTTPS:443 | 建议使用 Azure Monitor 进行正确的指标和监视。 |
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | 此地址用于正确操作 Azure 策略 (目前在 AKS 中为预览版)。 |
| apt.dockerproject.org                   | HTTPS:443 | 此地址用于在基于 GPU 的节点上进行正确的驱动程序安装和操作。 |
| nvidia.github.io                        | HTTPS:443 | 此地址用于在基于 GPU 的节点上进行正确的驱动程序安装和操作。 |

## <a name="next-steps"></a>后续步骤

本文介绍了在限制群集的出口流量时允许哪些端口和地址。 你还可以定义 pod 本身可以进行通信的方式, 以及它们在群集中的限制。 有关详细信息, 请参阅[使用 AKS 中的网络策略在 pod 之间进行安全通信][network-policy]。

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
