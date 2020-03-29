---
title: 限制 Azure 库伯内斯服务 （AKS） 中的出口流量
description: 了解在 Azure 库伯奈斯服务 （AKS） 中控制出口流量需要哪些端口和地址
services: container-service
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: 2cd7aeea272d22615d3ba3d3db6acc2c84d22cca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080188"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>控制 Azure 库伯内斯服务 （AKS） 中群集节点的传出流量

默认情况下，AKS 群集具有不受限制的出站（出口）互联网接入。 此级别的网络访问允许运行的节点和服务根据需要访问外部资源。 如果要限制出口流量，则必须访问数量有限的端口和地址，以保持健康的群集维护任务。 默认情况下，群集配置为仅使用来自 Microsoft 容器注册表 （MCR） 或 Azure 容器注册表 （ACR） 的基本系统容器映像。 配置首选的防火墙和安全规则以允许这些所需的端口和地址。

本文详细介绍了在 AKS 群集中限制出口流量时，需要哪些网络端口和完全限定的域名 （FQDN） 是必需的，可选。

> [!IMPORTANT]
> 本文档仅介绍如何锁定离开 AKS 子网的流量。 AKS 没有入口要求。  不支持使用网络安全组 （NSG） 和防火墙阻止内部子网流量。 要控制和阻止群集中的流量，请使用[网络策略][network-policy]。

## <a name="before-you-begin"></a>开始之前

您需要 Azure CLI 版本 2.0.66 或更高版本安装和配置。 运行 `az --version` 即可查找版本。 如果需要安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

## <a name="egress-traffic-overview"></a>出口流量概述

出于管理和操作目的，AKS 群集中的节点需要访问某些端口和完全限定的域名 （FQDN）。 这些操作可以是与 API 服务器通信，或者下载并安装核心 Kubernetes 群集组件和节点安全更新。 默认情况下，AKS 群集中的节点的出入口（出站）Internet 流量不受限制。 群集可以从外部存储库中提取基本系统容器映像。

为了提高 AKS 群集的安全性，您可能希望限制出口流量。 群集配置为从 MCR 或 ACR 中提取基本系统容器映像。 如果以这种方式锁定出口流量，请定义特定端口和 FQDN，以允许 AKS 节点与所需的外部服务正确通信。 如果没有这些授权端口和 FQDN，您的 AKS 节点无法与 API 服务器通信或安装核心组件。

您可以使用[Azure 防火墙][azure-firewall]或第三方防火墙设备来保护出口流量并定义这些所需的端口和地址。 AKS 不会为您自动创建这些规则。 在网络防火墙中创建适当的规则时，以下端口和地址可供参考。

> [!IMPORTANT]
> 当您使用 Azure 防火墙限制出口流量并创建用户定义的路由 （UDR） 以强制所有出口流量时，请确保在防火墙中创建适当的 DNAT 规则以正确允许入口流量。 将 Azure 防火墙与 UDR 一起使用会破坏由于非对称路由而导致的入口设置。 （如果 AKS 子网具有通往防火墙专用 IP 地址的默认路由，但您使用的是公共负载均衡器 - 入口或 Kubernetes 类型的服务：LoadBalancer），则会出现此问题。 在这种情况下，将通过负载均衡器的公共 IP 地址接收传入的负载均衡器流量，但返回路径将通过防火墙的专用 IP 地址。 由于防火墙是有状态的，因此它会丢弃返回的数据包，因为防火墙不知道已建立的会话。 要了解如何将 Azure 防火墙与入口或服务负载均衡器集成，请参阅将[Azure 防火墙与 Azure 标准负载均衡器集成](https://docs.microsoft.com/azure/firewall/integrate-lb)。
> 您可以使用出口辅助节点 IP 和 API 服务器的 IP 之间的网络规则锁定 TCP 端口 9000、TCP 端口 22 和 UDP 端口 1194 的流量。

在 AKS 中，有两组端口和地址：

* [AKS 群集所需的端口和地址](#required-ports-and-addresses-for-aks-clusters)详细说明了授权出口流量的最低要求。
* [AKS 群集的可选建议地址和端口](#optional-recommended-addresses-and-ports-for-aks-clusters)并非所有方案都是必需的，但与其他服务（如 Azure 监视器）的集成将无法正常工作。 查看可选端口和 FQDN 的列表，并授权 AKS 群集中使用的任何服务和组件。

> [!NOTE]
> 限制出口流量仅适用于新的 AKS 群集。 对于现有群集，在限制出口流量之前，请使用`az aks upgrade`命令[执行群集升级操作][aks-upgrade]。

## <a name="required-ports-and-addresses-for-aks-clusters"></a>AKS 群集的所需端口和地址

AKS 群集需要以下出站端口/网络规则：

* TCP 端口*443*
* 如果您有一个需要与 API 服务器对话的应用，则需要 TCP [IPAddrofapiServer]：443。  可以在创建群集后设置此更改。
* TCP 端口*9000、TCP*端口*22*和 UDP 端口*1194，* 用于隧道前舱与 API 服务器上的隧道端通信。
    * 要获得更具体的了解，请参阅 @*.hcp。\<位置\>.azmk8s.io*和 **.tun。\<下表\>中的位置 .azmk8s.io*地址。
* 用于网络时间协议 （NTP） 时间同步（Linux 节点）的 UDP 端口*123。*
* 如果您有直接访问 API 服务器的 pod，则还需要用于 DNS 的 UDP 端口*53。*

需要以下 FQDN / 应用程序规则：

> [!IMPORTANT]
> ***.blob.core.windows.net和aksrepos.azurecr.io**不再需要 FQDN 规则即可进行出口锁定。  对于现有群集，使用`az aks upgrade`命令[执行群集升级操作][aks-upgrade]以删除这些规则。

> [!IMPORTANT]
> *.cdn.mscr.io已替换为 Azure 公共云区域的 *.data.mcr.microsoft.com。 请升级现有的防火墙规则，以便更改生效。

- Azure 全球

| FQDN                       | 端口      | 用途      |
|----------------------------|-----------|----------|
| *.hcp.\<位置\>.azmk8s.io | HTTPS：443， TCP：22， TCP：9000， UDP：1194 | 节点<> API 服务器通信需要此地址。 将*\<位置\>* 替换为部署 AKS 群集的区域。 |
| \.tun.\<位置\>.azmk8s.io | HTTPS：443， TCP：22， TCP：9000， UDP：1194 | 节点<> API 服务器通信需要此地址。 将*\<位置\>* 替换为部署 AKS 群集的区域。 |
| *.cdn.mscr.io       | HTTPS：443 | 此地址对于 Azure 内容传递网络 （CDN） 支持的 MCR 存储是必需的。 |
| mcr.microsoft.com          | HTTPS：443 | 访问 Microsoft 容器注册表 （MCR） 中的图像需要此地址。 此注册表包含群集在升级和扩展期间运行群集所需的第一方图像/图表（例如，moby 等） |
| *.data.mcr.microsoft.com             | HTTPS：443 | 此地址对于由 Azure 内容传递网络 （CDN） 支持的 MCR 存储是必需的。 |
| management.azure.com       | HTTPS：443 | 库伯内斯 GET/PUT 操作需要此地址。 |
| login.microsoftonline.com  | HTTPS：443 | Azure 活动目录身份验证需要此地址。 |
| ntp.ubuntu.com             | UDP：123   | 此地址是 Linux 节点上的 NTP 时间同步所必需的。 |
| packages.microsoft.com     | HTTPS：443 | 此地址是用于缓存*的 apt-get*操作的 Microsoft 包存储库。  示例包包括 Moby、PowerShell 和 Azure CLI。 |
| acs-mirror.azureedge.net      | HTTPS：443 | 此地址用于安装所需的二进制文件（如 kubenet 和 Azure CNI）所需的存储库。 |

- Azure 中国世纪互联

| FQDN                       | 端口      | 用途      |
|----------------------------|-----------|----------|
| *.hcp.\<位置\>.cx.prod.service.azk8s.cn | HTTPS：443， TCP：22， TCP：9000， UDP：1194 | 节点<> API 服务器通信需要此地址。 将*\<位置\>* 替换为部署 AKS 群集的区域。 |
| \.tun.\<位置\>.cx.prod.service.azk8s.cn | HTTPS：443， TCP：22， TCP：9000， UDP：1194 | 节点<> API 服务器通信需要此地址。 将*\<位置\>* 替换为部署 AKS 群集的区域。 |
| *.azk8s.cn        | HTTPS：443 | 下载所需的二进制文件和图像需要此地址|
| mcr.microsoft.com          | HTTPS：443 | 访问 Microsoft 容器注册表 （MCR） 中的图像需要此地址。 此注册表包含群集在升级和扩展期间运行群集所需的第一方图像/图表（例如，moby 等） |
| *.cdn.mscr.io       | HTTPS：443 | 此地址对于 Azure 内容传递网络 （CDN） 支持的 MCR 存储是必需的。 |
| *.data.mcr.microsoft.com             | HTTPS：443 | 此地址对于由 Azure 内容传递网络 （CDN） 支持的 MCR 存储是必需的。 |
| management.chinacloudapi.cn       | HTTPS：443 | 库伯内斯 GET/PUT 操作需要此地址。 |
| login.chinacloudapi.cn  | HTTPS：443 | Azure 活动目录身份验证需要此地址。 |
| ntp.ubuntu.com             | UDP：123   | 此地址是 Linux 节点上的 NTP 时间同步所必需的。 |
| packages.microsoft.com     | HTTPS：443 | 此地址是用于缓存*的 apt-get*操作的 Microsoft 包存储库。  示例包包括 Moby、PowerShell 和 Azure CLI。 |

- Azure Government 

| FQDN                       | 端口      | 用途      |
|----------------------------|-----------|----------|
| *.hcp.\<位置\>.cx.aks.containerservice.azure.us | HTTPS：443， TCP：22， TCP：9000， UDP：1194 | 节点<> API 服务器通信需要此地址。 将*\<位置\>* 替换为部署 AKS 群集的区域。 |
| \.tun.\<位置\>.cx.aks.containerservice.azure.us | HTTPS：443， TCP：22， TCP：9000， UDP：1194 | 节点<> API 服务器通信需要此地址。 将*\<位置\>* 替换为部署 AKS 群集的区域。 |
| mcr.microsoft.com          | HTTPS：443 | 访问 Microsoft 容器注册表 （MCR） 中的图像需要此地址。 此注册表包含群集在升级和扩展期间运行群集所需的第一方图像/图表（例如，moby 等） |
|*.cdn.mscr.io              | HTTPS：443 | 此地址对于 Azure 内容传递网络 （CDN） 支持的 MCR 存储是必需的。 |
| *.data.mcr.microsoft.com             | HTTPS：443 | 此地址对于由 Azure 内容传递网络 （CDN） 支持的 MCR 存储是必需的。 |
| management.usgovcloudapi.net       | HTTPS：443 | 库伯内斯 GET/PUT 操作需要此地址。 |
| login.microsoftonline.us  | HTTPS：443 | Azure 活动目录身份验证需要此地址。 |
| ntp.ubuntu.com             | UDP：123   | 此地址是 Linux 节点上的 NTP 时间同步所必需的。 |
| packages.microsoft.com     | HTTPS：443 | 此地址是用于缓存*的 apt-get*操作的 Microsoft 包存储库。  示例包包括 Moby、PowerShell 和 Azure CLI。 |
| acs-mirror.azureedge.net      | HTTPS：443 | 此地址用于安装所需的二进制文件（如 kubenet 和 Azure CNI）所需的存储库。 |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>AKS 群集的可选推荐地址和端口

以下出站端口/网络规则对于 AKS 群集是可选的：

建议以下 FQDN / 应用程序规则使 AKS 群集正常运行：

| FQDN                                    | 端口      | 用途      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com，azure.archive.ubuntu.com，changelogs.ubuntu.com | HTTP：80   | 此地址允许 Linux 群集节点下载所需的安全修补程序和更新。 |

## <a name="required-addresses-and-ports-for-gpu-enabled-aks-clusters"></a>启用 GPU 的 AKS 群集所需的地址和端口

启用 GPU 的 AKS 群集需要以下 FQDN / 应用程序规则：

| FQDN                                    | 端口      | 用途      |
|-----------------------------------------|-----------|----------|
| nvidia.github.io | HTTPS：443 | 此地址用于在基于 GPU 的节点上正确安装和操作驱动程序。 |
| us.download.nvidia.com | HTTPS：443 | 此地址用于在基于 GPU 的节点上正确安装和操作驱动程序。 |
| apt.dockerproject.org | HTTPS：443 | 此地址用于在基于 GPU 的节点上正确安装和操作驱动程序。 |

## <a name="required-addresses-and-ports-with-azure-monitor-for-containers-enabled"></a>启用了启用容器的 Azure 监视器所需的地址和端口

启用容器的 Azure 监视器的 AKS 群集需要以下 FQDN / 应用程序规则：

| FQDN                                    | 端口      | 用途      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | HTTPS：443    | 这是用于使用 Azure 监视器进行正确的指标和监视遥测。 |
| *.ods.opinsights.azure.com    | HTTPS：443    | Azure 监视器使用此选项来引入日志分析数据。 |
| *.oms.opinsights.azure.com | HTTPS：443 | 此地址由 omsagent 使用，用于对日志分析服务进行身份验证。 |
|*.microsoftonline.com | HTTPS：443 | 这用于验证指标并将其发送到 Azure 监视器。 |
|*.monitoring.azure.com | HTTPS：443 | 这用于向 Azure 监视器发送指标数据。 |

## <a name="required-addresses-and-ports-with-azure-dev-spaces-enabled"></a>启用 Azure 开发空间所需的地址和端口

启用 Azure 开发空间的 AKS 群集需要以下 FQDN / 应用程序规则：

| FQDN                                    | 端口      | 用途      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS：443 | 此地址用于拉 linux 高山和其他 Azure 开发人员空间图像 |
| gcr.io | HTTP：443 | 此地址用于拉舵/牵引器图像 |
| storage.googleapis.com | HTTP：443 | 此地址用于拉舵/牵引器图像 |
| 阿兹兹-\<吉德\>.\<位置\>.azds.io | HTTPS：443 | 与控制器的 Azure 开发空间后端服务进行通信。 确切的 FQDN 可在 %USERPROFILE%\.azds_s_json 中的"数据平面Fqdn"中找到。 |

## <a name="required-addresses-and-ports-for-aks-clusters-with-azure-policy-in-public-preview-enabled"></a>启用 Azure 策略（在公共预览版中）的 AKS 群集所需的地址和端口

> [!CAUTION]
> 以下部分功能处于预览状态。  随着功能移动到公共预览和将来发布阶段，本文中的建议可能会发生变化。

对于启用 Azure 策略的 AKS 群集，需要以下 FQDN / 应用程序规则。

| FQDN                                    | 端口      | 用途      |
|-----------------------------------------|-----------|----------|
| gov-prod-policy-data.trafficmanager.net | HTTPS：443 | 此地址用于正确操作 Azure 策略。 （当前在 AKS 预览版中） |
| raw.githubusercontent.com | HTTPS：443 | 此地址用于从 GitHub 中提取内置策略，以确保 Azure 策略的正确操作。 （当前在 AKS 预览版中） |
| \.gk.\<位置\>.azmk8s.io | HTTPS：443    | Azure 策略加载项，该加载项与在主服务器中运行的网守审核终结点对话，以获得审核结果。 |
| dc.services.visualstudio.com | HTTPS：443 | 向应用程序见解终结点发送遥测数据的 Azure 策略加载项。 |

## <a name="required-by-windows-server-based-nodes-in-public-preview-enabled"></a>启用基于 Windows 服务器的节点（在公共预览中）所需的

> [!CAUTION]
> 以下部分功能处于预览状态。  随着功能移动到公共预览和将来发布阶段，本文中的建议可能会发生变化。

基于 Windows 服务器 AKS 群集需要以下 FQDN / 应用程序规则：

| FQDN                                    | 端口      | 用途      |
|-----------------------------------------|-----------|----------|
| onegetcdn.azureedge.net、winlayers.blob.core.windows.net、winlayers.cdn.mscr.io、go.microsoft.com | HTTPS：443 | 安装与窗口相关的二进制文件 |
| mp.microsoft.com，www.msftconnecttest.com，ctldl.windowsupdate.com<span></span> | HTTP：80 | 安装与窗口相关的二进制文件 |
| kms.core.windows.net | TCP：1688 | 安装与窗口相关的二进制文件 |


## <a name="next-steps"></a>后续步骤

在本文中，您了解了如果限制群集的出口流量，允许哪些端口和地址。 您还可以定义窗格本身如何进行通信，以及它们在群集中有哪些限制。 有关详细信息，请参阅使用[AKS 中的网络策略在 pod 之间安全流量][network-policy]。

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
