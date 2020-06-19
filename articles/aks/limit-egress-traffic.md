---
title: 限制 Azure Kubernetes 服务 (AKS) 中的出口流量
description: 了解控制 Azure Kubernetes 服务 (AKS) 中的出口流量所需的端口和地址
services: container-service
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: 194e799daf107220c28404001d223e521dceeb3f
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83870904"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>控制 Azure Kubernetes 服务 (AKS) 中群集节点的出口流量

默认情况下，AKS 群集具有不受限制的出站（出口）internet 访问权限。 此级别的网络访问权限允许运行的节点和服务根据需要访问外部资源。 如果要限制出口流量，则必须访问有限数量的端口和地址才能维护正常的群集维护任务。 默认情况下，群集配置为仅使用来自 Microsoft 容器注册表 (MCR) 或 Azure 容器注册表 (ACR) 的基本系统容器映像。 配置首选的防火墙和安全规则以允许这些必需的端口和地址。

本文详细介绍了如果限制 AKS 群集中的出口流量，则有哪些必需和可选的网络端口和完全限定的域名 (FQDN)。

> [!IMPORTANT]
> 本文档仅介绍如何锁定离开 AKS 子网的流量。 AKS 没有入口要求。  不支持使用网络安全组 (NSG) 和防火墙阻止内部子网流量。 若要控制和阻止群集中的流量，请使用[网络策略][network-policy]。

## <a name="before-you-begin"></a>开始之前

需要安装并配置 Azure CLI 2.0.66 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

## <a name="egress-traffic-overview"></a>出口流量概述

出于管理和操作目的，AKS 群集中的节点需要访问某些端口和完全限定的域名 (FQDN)。 这些操作可以是与 API 服务器通信，或者下载并安装核心 Kubernetes 群集组件和节点安全更新。 默认情况下，对于 AKS 群集中的节点，出口（出站）Internet 流量不受限制。 群集可以从外部存储库中提取基本系统容器映像。

为了提高 AKS 群集的安全性，可能需要限制出口流量。 群集配置为从 MCR 或 ACR 中提取基本系统容器映像。 如果以这种方式锁定出口流量，请定义特定端口和 FQDN，以允许 AKS 节点与所需的外部服务正确通信。 如果没有这些授权端口和 FQDN，AKS 节点将无法与 API 服务器通信或安装核心组件。

可以使用 [Azure 防火墙][azure-firewall]或第三方防火墙设备来保护出口流量，并定义这些必需的端口和地址。 AKS 不会自动为你创建这些规则。 在网络防火墙中创建相应规则时，可以参考以下端口和地址。

> [!IMPORTANT]
> 当使用 Azure 防火墙来限制出口流量并创建一个用户定义的路由 (UDR) 来强制应用于所有出口流量时，请确保在防火墙中创建了适当的 DNAT 规则来正确地允许入口流量。 使用带有 UDR 的 Azure 防火墙会由于非对称路由而中断入口设置。 （如果 AKS 子网的默认路由指向防火墙的专用 IP 地址，但使用的是公共负载均衡器 - 传入或 Kubernetes 服务类型：LoadBalancer）。 在这种情况下，将通过负载均衡器的公共 IP 地址接收传入的负载均衡器流量，但返回路径将通过防火墙的专用 IP 地址。 由于防火墙是有状态的，并且无法识别已建立的会话，因此会丢弃返回的数据包。 若要了解如何将 Azure 防火墙与入口或服务负载均衡器集成，请参阅[将 Azure 防火墙与 Azure 标准负载均衡器集成](https://docs.microsoft.com/azure/firewall/integrate-lb)。
> 使用出口工作器节点 IP 和 API 服务器 IP 之间的网络规则，可以锁定 TCP 端口 9000、TCP 端口 22 和 UDP 端口 1194 的流量。

在 AKS 中，有两组端口和地址：

* [AKS 群集所需的端口和地址](#required-ports-and-addresses-for-aks-clusters)详细说明了授权出口流量的最低要求。
* 并非所有方案都需要 [AKS 群集的可选推荐地址和端口](#optional-recommended-addresses-and-ports-for-aks-clusters)，但与其他服务（如 Azure Monitor）的集成将无法正常工作。 查看此可选端口和 FQDN 列表，并授权 AKS 群集中使用的任何服务和组件。

> [!NOTE]
> 限制出口流量仅适用于新的 AKS 群集。 对于现有群集，在限制出口流量之前，请使用 `az aks upgrade` 命令[执行群集升级操作][aks-upgrade]。

## <a name="required-ports-and-addresses-for-aks-clusters"></a>AKS 群集的所需端口和地址

AKS 群集需要以下出站端口/网络规则：

* TCP 端口 443
* 如果有应用需要与 API 服务器对话，则需要 TCP [IPAddrOfYourAPIServer]:443。  此更改可以在创建群集后设置。
* TCP 端口 9000、TCP 端口 22 和 UDP 端口 1194，用于隧道前 Pod 与 API 服务器上的隧道末端通信  。
    * 若要获得更具体的信息，请参见下表中的 *.hcp.\<location\>.azmk8s.io 和 *.tun.\<location\>.azmk8s.io 地址 。
* 用于网络时间协议 (NTP) 时间同步 (Linux 节点) 的 UDP 端口 123。
* 如果你有直接访问 API 服务器的 Pod，则还需要用于 DNS 的 UDP 端口 53。

需要以下 FQDN/应用程序规则：

> [!IMPORTANT]
> *.blob.core.windows.net 和 aksrepos.azurecr.io 不再需要出口锁定的 FQDN 规则。  对于现有群集，使用 `az aks upgrade` 命令[执行群集升级操作][aks-upgrade]以删除这些规则。

> [!IMPORTANT]
> 对于Azure公共云区域，*.cdn.mscr.io 已被 *.data.mcr.microsoft.com 替换。 请升级现有防火墙规则以使更改生效。

- Azure 全球

| FQDN                       | 端口      | 用途      |
|----------------------------|-----------|----------|
| *.hcp.\<location\>.azmk8s.io | HTTPS:443，TCP:22，TCP:9000，UDP:1194 | 节点 <-> API 服务器通信需要使用此地址。 将 \<location\> 替换为部署 AKS 群集的区域。 |
| *.tun.\<location\>.azmk8s.io | HTTPS:443，TCP:22，TCP:9000，UDP:1194 | 节点 <-> API 服务器通信需要使用此地址。 将 \<location\> 替换为部署 AKS 群集的区域。 |
| *.cdn.mscr.io       | HTTPS:443 | Azure 内容分发网络 (CDN) 支持的 MCR 存储需要此地址。 |
| mcr.microsoft.com          | HTTPS:443 | 访问 Microsoft 容器注册表 (MCR) 中的映像需要此地址。 此注册表包含在群集升级和缩放期间群集运行所需的第一方映像/图表（例如，moby 等） |
| *.data.mcr.microsoft.com             | HTTPS:443 | Azure 内容分发网络 (CDN) 支持的 MCR 存储需要此地址。 |
| management.azure.com       | HTTPS:443 | Kubernetes GET/PUT 操作需要使用此地址。 |
| login.microsoftonline.com  | HTTPS:443 | Azure Active Directory 身份验证需要使用此地址。 |
| ntp.ubuntu.com             | UDP:123   | Linux 节点上 NTP 时间同步需要使用此地址。 |
| packages.microsoft.com     | HTTPS:443 | 此地址是用于缓存 apt-get 操作的 Microsoft 包存储库。  示例包包括 Moby、PowerShell 和 Azure CLI。 |
| acs-mirror.azureedge.net      | HTTPS:443 | 此地址用于安装所需二进制文件（如 kubenet 和 Azure CNI）所需的存储库。 |

- Azure 中国世纪互联

| FQDN                       | 端口      | 用途      |
|----------------------------|-----------|----------|
| *.hcp.\<location\>.cx.prod.service.azk8s.cn | HTTPS:443，TCP:22，TCP:9000，UDP:1194 | 节点 <-> API 服务器通信需要使用此地址。 将 \<location\> 替换为部署 AKS 群集的区域。 |
| *.tun.\<location\>.cx.prod.service.azk8s.cn | HTTPS:443，TCP:22，TCP:9000，UDP:1194 | 节点 <-> API 服务器通信需要使用此地址。 将 \<location\> 替换为部署 AKS 群集的区域。 |
| *.azk8s.cn        | HTTPS:443 | 下载所需的二进制文件和映像需要此地址|
| mcr.microsoft.com          | HTTPS:443 | 访问 Microsoft 容器注册表 (MCR) 中的映像需要此地址。 此注册表包含在群集升级和缩放期间群集运行所需的第一方映像/图表（例如，moby 等） |
| *.cdn.mscr.io       | HTTPS:443 | Azure 内容分发网络 (CDN) 支持的 MCR 存储需要此地址。 |
| *.data.mcr.microsoft.com             | HTTPS:443 | Azure 内容分发网络 (CDN) 支持的 MCR 存储需要此地址。 |
| management.chinacloudapi.cn       | HTTPS:443 | Kubernetes GET/PUT 操作需要使用此地址。 |
| login.chinacloudapi.cn  | HTTPS:443 | Azure Active Directory 身份验证需要使用此地址。 |
| ntp.ubuntu.com             | UDP:123   | Linux 节点上 NTP 时间同步需要使用此地址。 |
| packages.microsoft.com     | HTTPS:443 | 此地址是用于缓存 apt-get 操作的 Microsoft 包存储库。  示例包包括 Moby、PowerShell 和 Azure CLI。 |

- Azure Government

| FQDN                       | 端口      | 用途      |
|----------------------------|-----------|----------|
| *.hcp.\<location\>.cx.aks.containerservice.azure.us | HTTPS:443，TCP:22，TCP:9000，UDP:1194 | 节点 <-> API 服务器通信需要使用此地址。 将 \<location\> 替换为部署 AKS 群集的区域。 |
| *.tun.\<location\>.cx.aks.containerservice.azure.us | HTTPS:443，TCP:22，TCP:9000，UDP:1194 | 节点 <-> API 服务器通信需要使用此地址。 将 \<location\> 替换为部署 AKS 群集的区域。 |
| mcr.microsoft.com          | HTTPS:443 | 访问 Microsoft 容器注册表 (MCR) 中的映像需要此地址。 此注册表包含在群集升级和缩放期间群集运行所需的第一方映像/图表（例如，moby 等） |
|*.cdn.mscr.io              | HTTPS:443 | Azure 内容分发网络 (CDN) 支持的 MCR 存储需要此地址。 |
| *.data.mcr.microsoft.com             | HTTPS:443 | Azure 内容分发网络 (CDN) 支持的 MCR 存储需要此地址。 |
| management.usgovcloudapi.net       | HTTPS:443 | Kubernetes GET/PUT 操作需要使用此地址。 |
| login.microsoftonline.us  | HTTPS:443 | Azure Active Directory 身份验证需要使用此地址。 |
| ntp.ubuntu.com             | UDP:123   | Linux 节点上 NTP 时间同步需要使用此地址。 |
| packages.microsoft.com     | HTTPS:443 | 此地址是用于缓存 apt-get 操作的 Microsoft 包存储库。  示例包包括 Moby、PowerShell 和 Azure CLI。 |
| acs-mirror.azureedge.net      | HTTPS:443 | 此地址用于安装所需二进制文件（如 kubenet 和 Azure CNI）所需的存储库。 |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>AKS 群集的可选推荐地址和端口

以下出站端口/网络规则对于 AKS 群集是可选的：

建议使用以下 FQDN/应用程序规则，以使 AKS 群集正常工作：

| FQDN                                    | 端口      | 用途      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com, azure.archive.ubuntu.com, changelogs.ubuntu.com | HTTP:80   | 此地址允许 Linux 群集节点下载所需的安全修补程序和更新。 |

## <a name="required-addresses-and-ports-for-gpu-enabled-aks-clusters"></a>启用 GPU 的 AKS 群集所需的地址和端口

启用了 GPU 的 AKS 群集需要以下 FQDN/应用程序规则：

| FQDN                                    | 端口      | 用途      |
|-----------------------------------------|-----------|----------|
| nvidia.github.io | HTTPS:443 | 此地址用于在基于 GPU 的节点上正确安装和操作驱动程序。 |
| us.download.nvidia.com | HTTPS:443 | 此地址用于在基于 GPU 的节点上正确安装和操作驱动程序。 |
| apt.dockerproject.org | HTTPS:443 | 此地址用于在基于 GPU 的节点上正确安装和操作驱动程序。 |

## <a name="required-addresses-and-ports-with-azure-monitor-for-containers-enabled"></a>启用适用于容器的 Azure Monitor 所需的地址和端口

启用适用于容器的 Azure Monitor 的 AKS 群集需要以下 FQDN/应用程序规则：

| FQDN                                    | 端口      | 用途      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | HTTPS:443    | 这是为了使用 Azure Monitor 进行正确的指标和监视遥测。 |
| *.ods.opinsights.azure.com    | HTTPS:443    | Azure Monitor 使用它来获取日志分析数据。 |
| *.oms.opinsights.azure.com | HTTPS:443 | 此地址由 omsagent 使用，omsagent 用于验证日志分析服务。 |
|*.microsoftonline.com | HTTPS:443 | 这用于身份验证并向 Azure Monitor 发送指标。 |
|*.monitoring.azure.com | HTTPS:443 | 这用于将指标数据发送到 Azure Monitor。 |

## <a name="required-addresses-and-ports-with-azure-dev-spaces-enabled"></a>启用 Azure Dev Spaces 所需的地址和端口

启用 Azure Dev Spaces 的 AKS 群集需要以下 FQDN/应用程序规则：

| FQDN                                    | 端口      | 用途      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | 此地址用于拉取 linux alpine 和其他 Azure Dev Spaces 映像 |
| gcr.io | HTTPS:443 | 此地址用于拉取 helm/tiller 映像 |
| storage.googleapis.com | HTTPS:443 | 此地址用于拉取 helm/tiller 映像 |
| azds-\<guid\>.\<location\>.azds.io | HTTPS:443 | 用于与控制器的 Azure Dev Spaces 后端服务进行通信。 可以在 %USERPROFILE%\.azds\settings.json 的“dataplaneFqdn”中找到确切的 FQDN |

## <a name="required-addresses-and-ports-for-aks-clusters-with-azure-policy-in-public-preview-enabled"></a>启用 Azure Policy（在公共预览中）的 AKS 群集所需的地址和端口

> [!CAUTION]
> 下面的某些功能处于预览阶段。  随着该功能进入公开预览和未来发布阶段，本文中的建议可能会发生变化。

启用 Azure Policy 的 AKS 群集需要以下 FQDN/应用程序规则。

| FQDN                                    | 端口      | 用途      |
|-----------------------------------------|-----------|----------|
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | 此地址用于 Azure Policy 的正确操作。 （目前在 AKS 中以预览版提供。） |
| raw.githubusercontent.com | HTTPS:443 | 此地址用于从 GitHub 中提取内置策略，以确保 Azure Policy 的正确操作。 （目前在 AKS 中以预览版提供。） |
| *.gk.\<location\>.azmk8s.io | HTTPS:443    | 与主服务器中运行的 Gatekeeper 审核终结点对话以获取审核结果的 Azure Policy 加载项。 |
| dc.services.visualstudio.com | HTTPS:443 | 将遥测数据发送到 Applications Insights 终结点的 Azure Policy 加载项。 |

## <a name="required-by-windows-server-based-nodes-enabled"></a>启用基于 Windows 服务器的节点所需

使用基于 Windows 服务器的节点池需要以下 FQDN/应用程序规则：

| FQDN                                    | 端口      | 用途      |
|-----------------------------------------|-----------|----------|
| onegetcdn.azureedge.net，winlayers.blob.core.windows.net，winlayers.cdn.mscr.io，go.microsoft.com | HTTPS:443 | 安装与 windows 相关的二进制文件 |
| mp.microsoft.com, www<span></span>.msftconnecttest.com, ctldl.windowsupdate.com | HTTP:80 | 安装与 windows 相关的二进制文件 |
| kms.core.windows.net | TCP:1688 | 安装与 windows 相关的二进制文件 |

## <a name="next-steps"></a>后续步骤

本文介绍了如果限制群集的出口流量，要允许哪些端口和地址。 你还可以定义 Pod 本身的通信方式，以及它们在群集中有哪些限制。 有关详细信息，请参阅[在 AKS 中使用网络策略保护 Pod 之间的流量][network-policy]。

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
