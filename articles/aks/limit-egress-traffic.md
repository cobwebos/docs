---
title: 限制 Azure Kubernetes Service （AKS）中的出口流量
description: 了解控制 Azure Kubernetes Service （AKS）中的出口流量所需的端口和地址
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/21/2020
ms.author: mlearned
ms.openlocfilehash: 1206c20ec4f547dd591ac711d546d1dad0b7a19a
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251594"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>控制 Azure Kubernetes Service （AKS）中群集节点的出口流量

默认情况下，AKS 群集具有不受限制的出站（传出） internet 访问权限。 此级别的网络访问权限允许运行的节点和服务根据需要访问外部资源。 如果希望限制传出流量，则必须能够访问有限数量的端口和地址才能维护正常的群集维护任务。 默认情况下，将群集配置为仅使用 Microsoft 容器注册表（MCR）或 Azure 容器注册表（ACR）中的基本系统容器映像。 配置首选的防火墙和安全规则，以允许所需的端口和地址。

本文详细介绍了需要哪些网络端口和完全限定的域名（Fqdn），并且如果在 AKS 群集中限制出站流量，则可以选择此选项。

> [!IMPORTANT]
> 本文档仅介绍如何锁定离开 AKS 子网的流量。 AKS 没有入口要求。  不支持使用网络安全组（Nsg）和防火墙阻止内部子网流量。 若要控制和阻止群集中的流量，请使用[网络策略][network-policy]。

## <a name="before-you-begin"></a>开始之前

需要安装并配置 Azure CLI 版本2.0.66 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

## <a name="egress-traffic-overview"></a>出口流量概述

出于管理和操作目的，AKS 群集中的节点需要访问特定端口和完全限定的域名（Fqdn）。 这些操作可以是与 API 服务器通信，或下载并安装 core Kubernetes 群集组件和节点安全更新。 默认情况下，AKS 群集中的节点不会限制传出（出站） internet 流量。 群集可能从外部存储库中提取基本系统容器映像。

若要提高 AKS 群集的安全性，您可能希望限制出站流量。 群集配置为从 MCR 或 ACR 请求基本系统容器映像。 如果以这种方式锁定出口流量，请定义特定端口和 Fqdn，以允许 AKS 节点与所需的外部服务正确通信。 如果没有这些授权端口和 Fqdn，则 AKS 节点无法与 API 服务器通信或安装核心组件。

可以使用[Azure 防火墙][azure-firewall]或第三方防火墙设备来保护出口流量，并定义这些所需的端口和地址。 AKS 不会自动为你创建这些规则。 当你在网络防火墙中创建适当的规则时，以下端口和地址用于引用。

> [!IMPORTANT]
> 使用 Azure 防火墙限制传出流量并创建用户定义的路由（UDR）来强制所有传出流量时，请确保在防火墙中创建适当的 DNAT 规则，以正确允许入口流量。 使用带有 UDR 的 Azure 防火墙会断开入口设置，因为不对称路由。 （如果 AKS 子网有一个默认路由，该路由转到防火墙的专用 IP 地址，但使用的是公共负载均衡器入口或类型为： LoadBalancer 的 Kubernetes 服务，则会出现此问题。 在这种情况下，将通过负载均衡器的公共 IP 地址接收传入的负载均衡器流量，但返回路径将通过防火墙的专用 IP 地址。 由于防火墙是有状态的，因此它会删除返回的数据包，因为防火墙无法识别已建立的会话。 若要了解如何将 Azure 防火墙与入口或服务负载均衡器集成，请参阅将[Azure firewall 与 azure 标准负载均衡器集成](https://docs.microsoft.com/azure/firewall/integrate-lb)。
> 你可以使用传出工作节点 IP 和 API 服务器 IP 之间的网络规则锁定 TCP 端口9000和 TCP 端口22的流量。

在 AKS 中，有两组端口和地址：

* [AKS 群集所需的端口和地址](#required-ports-and-addresses-for-aks-clusters)详细说明了授权出口流量的最低要求。
* 所有方案都不需要[可选的建议地址和端口用于 AKS 群集](#optional-recommended-addresses-and-ports-for-aks-clusters)，但与其他服务（如 Azure Monitor）的集成将不能正常工作。 查看此可选端口和 Fqdn 列表，并授权 AKS 群集中使用的任何服务和组件。

> [!NOTE]
> 限制传出流量仅适用于新的 AKS 群集。 对于现有群集，请使用 `az aks upgrade` 命令[执行群集升级操作][aks-upgrade]，然后才能限制传出流量。

## <a name="required-ports-and-addresses-for-aks-clusters"></a>AKS 群集所需的端口和地址

AKS 群集需要以下出站端口/网络规则：

* TCP 端口*443*
* TCP [IPAddrOfYourAPIServer]：如果有应用需要与 API 服务器通信，则需要443。  创建群集后，可以设置此更改。
* TCP 端口*9000*和 tcp 端口*22* （用于隧道前端盒）与 API 服务器上的隧道通信。
    * 若要获取更具体的信息，请参阅下表中的 * *\<位置\>azmk8s.io*和 * *\<. azmk8s.io 地址\>。*
* UDP 端口*123* ，适用于网络时间协议（NTP）时间同步（Linux 节点）。
* 如果你有直接访问 API 服务器的 pod，则还需要使用 UDP 端口*53*作为 DNS。

需要以下 FQDN/应用程序规则：

> [!IMPORTANT]
> **blob.core.windows.net 和 aksrepos.azurecr.io**不再需要用于出口锁定的 FQDN 规则。 *  对于现有群集，请使用 `az aks upgrade` 命令[执行群集升级操作][aks-upgrade]以删除这些规则。

- Azure 全球

| FQDN                       | 端口      | 用途      |
|----------------------------|-----------|----------|
| \* hcp。\<location\>. azmk8s.io | HTTPS：443，TCP：22，TCP：9000 | 此地址为 API 服务器终结点。 将 *\<位置*替换为部署 AKS 群集的区域\>。 |
| *. 运行。\<location\>. azmk8s.io | HTTPS：443，TCP：22，TCP：9000 | 此地址为 API 服务器终结点。 将 *\<位置*替换为部署 AKS 群集的区域\>。 |
| mcr.microsoft.com          | HTTPS:443 | 访问 Microsoft 容器注册表（MCR）中的图像需要此地址。 此注册表包含升级和缩放群集期间群集正常运行所需的第一方映像/图表（例如，小鲸鱼等） |
| *.cdn.mscr.io              | HTTPS:443 | Azure 内容分发网络（CDN）支持的 MCR 存储需要此地址。 |
| management.azure.com       | HTTPS:443 | 此地址是 Kubernetes GET/PUT 操作所必需的。 |
| login.microsoftonline.com  | HTTPS:443 | 此地址是 Azure Active Directory 身份验证所必需的。 |
| ntp.ubuntu.com             | UDP：123   | 对于 Linux 节点上的 NTP 时间同步，此地址是必需的。 |
| packages.microsoft.com     | HTTPS:443 | 此地址是用于缓存*apt*操作的 Microsoft 包存储库。  示例包包括小鲸鱼、PowerShell 和 Azure CLI。 |
| acs-mirror.azureedge.net   | HTTPS:443 | 此地址适用于安装所需的二进制文件（如 kubenet 和 Azure CNI）所需的存储库。 |

- Azure 中国世纪互联

| FQDN                       | 端口      | 用途      |
|----------------------------|-----------|----------|
| \* hcp。\<location\>. cx.prod.service.azk8s.cn | HTTPS：443，TCP：22，TCP：9000 | 此地址为 API 服务器终结点。 将 *\<位置*替换为部署 AKS 群集的区域\>。 |
| *. 运行。\<location\>. cx.prod.service.azk8s.cn | HTTPS：443，TCP：22，TCP：9000 | 此地址为 API 服务器终结点。 将 *\<位置*替换为部署 AKS 群集的区域\>。 |
| *. azk8s.cn        | HTTPS:443 | 下载所需的二进制文件和映像需要此地址|
| mcr.microsoft.com          | HTTPS:443 | 访问 Microsoft 容器注册表（MCR）中的图像需要此地址。 此注册表包含升级和缩放群集期间群集正常运行所需的第一方映像/图表（例如，小鲸鱼等） |
| *.cdn.mscr.io              | HTTPS:443 | Azure 内容分发网络（CDN）支持的 MCR 存储需要此地址。 |
| management.chinacloudapi.cn       | HTTPS:443 | 此地址是 Kubernetes GET/PUT 操作所必需的。 |
| login.chinacloudapi.cn  | HTTPS:443 | 此地址是 Azure Active Directory 身份验证所必需的。 |
| ntp.ubuntu.com             | UDP：123   | 对于 Linux 节点上的 NTP 时间同步，此地址是必需的。 |
| packages.microsoft.com     | HTTPS:443 | 此地址是用于缓存*apt*操作的 Microsoft 包存储库。  示例包包括小鲸鱼、PowerShell 和 Azure CLI。 |

- Azure Government

| FQDN                       | 端口      | 用途      |
|----------------------------|-----------|----------|
| \* hcp。\<location\>. cx.aks.containerservice.azure.us | HTTPS：443，TCP：22，TCP：9000 | 此地址为 API 服务器终结点。 将 *\<位置*替换为部署 AKS 群集的区域\>。 |
| *. 运行。\<location\>. cx.aks.containerservice.azure.us | HTTPS：443，TCP：22，TCP：9000 | 此地址为 API 服务器终结点。 将 *\<位置*替换为部署 AKS 群集的区域\>。 |
| mcr.microsoft.com          | HTTPS:443 | 访问 Microsoft 容器注册表（MCR）中的图像需要此地址。 此注册表包含升级和缩放群集期间群集正常运行所需的第一方映像/图表（例如，小鲸鱼等） |
| *.cdn.mscr.io              | HTTPS:443 | Azure 内容分发网络（CDN）支持的 MCR 存储需要此地址。 |
| management.usgovcloudapi.net       | HTTPS:443 | 此地址是 Kubernetes GET/PUT 操作所必需的。 |
| login.microsoftonline.us  | HTTPS:443 | 此地址是 Azure Active Directory 身份验证所必需的。 |
| ntp.ubuntu.com             | UDP：123   | 对于 Linux 节点上的 NTP 时间同步，此地址是必需的。 |
| packages.microsoft.com     | HTTPS:443 | 此地址是用于缓存*apt*操作的 Microsoft 包存储库。  示例包包括小鲸鱼、PowerShell 和 Azure CLI。 |
| acs-mirror.azureedge.net   | HTTPS:443 | 此地址适用于安装所需的二进制文件（如 kubenet 和 Azure CNI）所需的存储库。 |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>适用于 AKS 群集的可选推荐地址和端口

以下出站端口/网络规则对于 AKS 群集是可选的：

为了使 AKS 群集正常运行，建议使用以下 FQDN/应用程序规则：

| FQDN                                    | 端口      | 用途      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com、azure.archive.ubuntu.com、changelogs.ubuntu.com | HTTP:80   | 此地址允许 Linux 群集节点下载所需的安全修补程序和更新。 |

## <a name="required-addresses-and-ports-for-gpu-enabled-aks-clusters"></a>启用 GPU 的必需地址和端口 AKS 群集

启用了 GPU 的 AKS 群集需要以下 FQDN/应用程序规则：

| FQDN                                    | 端口      | 用途      |
|-----------------------------------------|-----------|----------|
| nvidia.github.io | HTTPS:443 | 此地址用于在基于 GPU 的节点上进行正确的驱动程序安装和操作。 |
| us.download.nvidia.com | HTTPS:443 | 此地址用于在基于 GPU 的节点上进行正确的驱动程序安装和操作。 |
| apt.dockerproject.org | HTTPS:443 | 此地址用于在基于 GPU 的节点上进行正确的驱动程序安装和操作。 |

## <a name="required-addresses-and-ports-with-azure-monitor-for-containers-enabled"></a>已启用容器 Azure Monitor 所需的地址和端口

启用容器 Azure Monitor 的 AKS 群集需要以下 FQDN/应用程序规则：

| FQDN                                    | 端口      | 用途      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | HTTPS:443  | 这适用于使用 Azure Monitor 的正确指标和监视遥测。 |
| *.ods.opinsights.azure.com    | HTTPS:443 | Azure Monitor 用于引入 log analytics 数据。 |
| *.oms.opinsights.azure.com | HTTPS:443 | 此地址由用于对 log analytics 服务进行身份验证的 omsagent 使用。 |
|*.microsoftonline.com | HTTPS:443 | 这用于验证度量值并将其发送到 Azure Monitor。 |
|*.monitoring.azure.com | HTTPS:443 | 此值用于将指标数据发送到 Azure Monitor。 |

## <a name="required-addresses-and-ports-with-azure-dev-spaces-enabled"></a>启用 Azure Dev Spaces 所需的地址和端口

启用了 Azure Dev Spaces 的 AKS 群集需要以下 FQDN/应用程序规则：

| FQDN                                    | 端口      | 用途      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | 此地址用于请求 linux alpine 和其他 Azure Dev Spaces 映像 |
| gcr.io | HTTP：443 | 此地址用于请求 helm/tiller 映像 |
| storage.googleapis.com | HTTP：443 | 此地址用于请求 helm/tiller 映像 |
| azds-<guid>.<location>.azds.io | HTTPS:443 | 与控制器的 Azure Dev Spaces 后端服务进行通信。 可以在% USERPROFILE%\.azds\settings.json 的 "dataplaneFqdn" 中找到准确的 FQDN。 |

## <a name="required-addresses-and-ports-for-aks-clusters-with-azure-policy-in-public-preview-enabled"></a>启用了 Azure 策略的 AKS 群集所需的地址和端口（在公共预览版中）

> [!CAUTION]
> 下面的某些功能处于预览阶段。  此文中的建议可能会随此功能在公共预览版和未来发布阶段的移动而改变。

启用了 Azure 策略的 AKS 群集需要以下 FQDN/应用程序规则。

| FQDN                                    | 端口      | 用途      |
|-----------------------------------------|-----------|----------|
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | 此地址用于正确操作 Azure 策略。 （目前在 AKS 中为预览版） |
| raw.githubusercontent.com | HTTPS:443 | 此地址用于从 GitHub 请求内置策略，以确保 Azure 策略的正确操作。 （目前在 AKS 中为预览版） |
| *.gk.<location>.azmk8s.io | HTTPS:443 | 与在主服务器上运行的网关审核终结点通信以获取审核结果的 Azure 策略附加项。 |
| dc.services.visualstudio.com | HTTPS:443 | 向 application insights 终结点发送遥测数据的 Azure 策略外接程序。 |

## <a name="required-by-windows-server-based-nodes-in-public-preview-enabled"></a>已启用基于 Windows Server 的节点（公共预览版中）的要求

> [!CAUTION]
> 下面的某些功能处于预览阶段。  此文中的建议可能会随此功能在公共预览版和未来发布阶段的移动而改变。

对于基于 Windows Server 的 AKS 群集，需要以下 FQDN/应用程序规则：

| FQDN                                    | 端口      | 用途      |
|-----------------------------------------|-----------|----------|
| onegetcdn.azureedge.net、winlayers.blob.core.windows.net、winlayers.cdn.mscr.io、go.microsoft.com | HTTPS:443 | 安装与 windows 相关的二进制文件 |
| mp.microsoft.com、<span></span>msftconnecttest.com、ctldl.windowsupdate.com | HTTP:80 | 安装与 windows 相关的二进制文件 |
| kms.core.windows.net | TCP：1688 | 安装与 windows 相关的二进制文件 |


## <a name="next-steps"></a>后续步骤

本文介绍了在限制群集的出口流量时允许哪些端口和地址。 你还可以定义 pod 本身可以进行通信的方式，以及它们在群集中的限制。 有关详细信息，请参阅[使用 AKS 中的网络策略在 pod 之间进行安全通信][network-policy]。

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
