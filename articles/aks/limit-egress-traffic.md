---
title: 将限制传出流量在 Azure Kubernetes 服务 (AKS)
description: 了解哪些端口和地址所需控制出口流量在 Azure Kubernetes 服务 (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/14/2019
ms.author: iainfou
ms.openlocfilehash: 13fbb20cde454a0aaab156a74a9fbcbac2d90d07
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66418129"
---
# <a name="preview---limit-egress-traffic-for-cluster-nodes-and-control-access-to-required-ports-and-services-in-azure-kubernetes-service-aks"></a>预览版-有关群集节点和控制对所需的端口和服务在 Azure Kubernetes 服务 (AKS) 的访问限制传出流量

默认情况下，AKS 群集中有不受限制的出站 （传出） internet 访问权限。 网络访问此级别允许节点和运行以根据需要访问外部资源的服务。 如果你想要将限制传出流量，有限的数量的端口和地址必须是可访问以保持正常运行的群集维护任务。 然后您的群集配置为仅使用从 Microsoft 容器注册表 (MCR) 或 Azure 容器注册表 (ACR) 不外部公共存储库的基础系统容器映像。

本文详细介绍哪些网络端口和完全限定的域名 (Fqdn) 的必需和可选如果你限制在 AKS 群集中的出口流量。  此功能目前处于预览状态。

> [!IMPORTANT]
> AKS 预览版功能是自助服务的选择加入。 提供这些项目是为了从我们的社区收集反馈和 bug。 在预览版中，这些功能不是用于生产环境中使用。 公共预览版中的功能属于最大努力支持。 AKS 技术支持团队的协助营业时间太平洋时区 （太平洋标准时间） 仅将提供。 有关其他信息，请参阅以下支持文章：
>
> * [AKS 支持策略][aks-support-policies]
> * [Azure 支持常见问题][aks-faq]

## <a name="before-you-begin"></a>开始之前

需要安装并配置 Azure CLI 2.0.61 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

若要创建的 AKS 群集，可以限制传出流量，请先启用你的订阅上的一个功能标志。 此功能注册配置任何 AKS 群集中创建要使用的基础系统从 MCR 或 ACR 的容器映像。 若要注册*AKSLockingDownEgressPreview*功能标志，请使用[az 功能注册][ az-feature-register]命令，在下面的示例所示：

```azurecli-interactive
az feature register --name AKSLockingDownEgressPreview --namespace Microsoft.ContainerService
```

状态显示为“已注册”需要几分钟时间  。 可以使用在注册状态检查[az 功能列表][ az-feature-list]命令：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSLockingDownEgressPreview')].{Name:name,State:properties.state}"
```

准备就绪后，刷新的注册*Microsoft.ContainerService*使用的资源提供程序[az provider register] [ az-provider-register]命令：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="egress-traffic-overview"></a>出口流量概述

有关管理和操作目的，AKS 群集中的节点需要访问特定端口和完全限定的域名 (Fqdn)。 这些操作可能要与其 API 服务器，或要下载并安装的核心 Kubernetes 群集组件和节点的安全更新。 默认情况下，传出 （出站） 的 internet 流量不受限制，AKS 群集中的节点。 群集可能会提取的基础系统容器映像从外部存储库。

若要增加 AKS 群集的安全，您可能希望限制传出流量。 群集配置为提取从 MCR 或 ACR 的容器映像的基础系统。 如果你锁定以这种方式的传出流量，必须定义特定的端口和以便正确地与所需的外部服务进行通信的 AKS 节点的 Fqdn。 如果没有这些授权的端口和 Fqdn，AKS 节点不能与 API 服务器进行通信或安装核心组件。

可以使用[Azure 防火墙][ azure-firewall]或第三方防火墙设备来保护你的出口流量并定义这些所需端口和地址。

在 AKS，有两个集的端口和地址：

* [AKS 群集所需端口和地址](#required-ports-and-addresses-for-aks-clusters)详细介绍了经过授权的传出流量的最低要求。
* [地址和端口的 AKS 群集中的可选建议](#optional-recommended-addresses-and-ports-for-aks-clusters)不需要的所有方案中，但与其他服务集成，例如 Azure Monitor 不会正常工作。 查看此列表的可选端口和 Fqdn，并授权的任何服务和在 AKS 群集中使用的组件。

> [!NOTE]
> 限制传出流量仅适用于新的 AKS 群集创建后启用功能标志注册。 对于现有群集，[执行群集升级操作][ aks-upgrade]使用`az aks upgrade`命令之前将限制传出流量。

## <a name="required-ports-and-addresses-for-aks-clusters"></a>所需的端口和地址为 AKS 群集

以下出站端口 / 网络规则所需的 AKS 群集：

* TCP 端口*443*
* TCP 端口*9000*和 TCP 端口*22*的隧道前端 pod 进行通信与 API 服务器上的隧道端。

以下 FQDN / 应用程序规则所需：

| FQDN                      | Port      | 用途      |
|---------------------------|-----------|----------|
| *.azmk8s.io               | HTTPS:443 | 此地址是 API 服务器终结点。 |
| aksrepos.azurecr.io       | HTTPS:443 | 此地址，则需要访问映像在 Azure 容器注册表 (ACR)。 |
| * .blob.core.windows.net   | HTTPS:443 | 此地址是映像存储在 ACR 中的后端存储区。 |
| mcr.microsoft.com         | HTTPS:443 | 此地址，则需要访问映像中 Microsoft 容器注册表 (MCR)。 |
| management.azure.com      | HTTPS:443 | 此地址是 Kubernetes GET/PUT 操作所必需的。 |
| login.microsoftonline.com | HTTPS:443 | 此地址是必需的 Azure Active Directory 身份验证。 |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>可选建议地址和端口为 AKS 群集

以下出站端口 / 不需要的 AKS 群集才能正常工作，但建议使用网络规则：

* UDP 端口*123* NTP 时间同步
* UDP 端口*53* dns

以下 FQDN / 应用程序规则建议用于 AKS 群集才能正常工作：

| FQDN                                    | Port      | 用途      |
|-----------------------------------------|-----------|----------|
| *.ubuntu.com                            | HTTP:80   | 此地址允许下载所需的安全修补程序和更新的 Linux 群集节点。 |
| packages.microsoft.com                  | HTTPS:443 | 此地址是 Microsoft 包存储库中使用的缓存*apt get*操作。 |
| dc.services.visualstudio.com            | HTTPS:443 | 建议用于正确的度量值和使用 Azure Monitor 进行监视。 |
| *.opinsights.azure.com                  | HTTPS:443 | 建议用于正确的度量值和使用 Azure Monitor 进行监视。 |
| *.monitoring.azure.com                  | HTTPS:443 | 建议用于正确的度量值和使用 Azure Monitor 进行监视。 |
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | 此地址用于 Azure 策略的 （目前以预览版在 AKS 中） 的正确操作。 |
| apt.dockerproject.org                   | HTTPS:443 | 此地址用于正确的驱动程序安装和基于 GPU 的节点上的操作。 |
| nvidia.github.io                        | HTTPS:443 | 此地址用于正确的驱动程序安装和基于 GPU 的节点上的操作。 |

## <a name="next-steps"></a>后续步骤

在本文中，您了解哪些端口和地址，从而允许则限制为群集的出口流量。 您还可以定义如何进行通信的 pod 本身以及设置什么限制必须在群集中。 有关详细信息，请参阅[保护在 AKS 中使用网络策略的 pod 之间的流量][network-policy]。

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
