---
title: 限制 Azure Kubernetes 服务 (AKS) 中的出口流量
description: 了解控制 Azure Kubernetes Service (AKS) 中的出口流量所需的端口和地址
services: container-service
ms.topic: article
ms.author: jpalma
ms.date: 06/29/2020
ms.custom: fasttrack-edit
author: palma21
ms.openlocfilehash: 00a20ece2358f0054e4490ffb914f78b82d9c509
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89594253"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>控制 Azure Kubernetes 服务 (AKS) 中群集节点的出口流量

本文提供了从 Azure Kubernetes 服务 (AKS) 保护出站流量的必要详细信息。 它包含基本 AKS 部署的群集要求，以及可选加载项和功能的其他要求。 [结尾处有一个示例，介绍如何使用 Azure 防火墙来配置这些要求](#restrict-egress-traffic-using-azure-firewall)。 但是，可以将此信息应用于任何出站限制方法或设备。

## <a name="background"></a>背景

AKS 群集部署在虚拟网络上。 此网络可以是托管的（由 AKS 创建），或者是自定义的（由用户预先配置）。 在这两种情况下，群集都对该虚拟网络外部的服务具有出站依赖项（该服务没有入站依赖项）。

为了便于管理和操作，AKS 群集中的节点需要访问特定的端口和完全限定的域名 (FQDN)。 节点与 API 服务器进行通信，或者下载并安装核心 Kubernetes 群集组件和节点安全更新都需要这些终结点。 例如，群集需要从 Microsoft 容器注册表 (MCR) 请求基础系统容器映像。

AKS 出站依赖项几乎完全是使用 FQDN 定义的，不附带任何静态地址。 缺少静态地址意味着无法使用网络安全组锁定来自 AKS 群集的出站流量。 

默认情况下，AKS 群集具有不受限制的出站（出口）Internet 访问权限。 此级别的网络访问权限允许运行的节点和服务根据需要访问外部资源。 如果希望限制出口流量，则必须限制可访问的端口和地址数量，才能维护正常的群集维护任务。 保护出站地址的最简单解决方案在于使用可基于域名控制出站流量的防火墙设备。 例如，Azure 防火墙可以根据目标的 FQDN 限制出站 HTTP 和 HTTPS 流量。 还可配置首选的防火墙和安全规则，以允许所需的端口和地址。

> [!IMPORTANT]
> 本文档仅介绍如何锁定离开 AKS 子网的流量。 默认情况下，AKS 没有入口需求。  不支持使用网络安全组 (NSG) 和防火墙阻止内部子网流量。 若要控制和阻止群集内的流量，请使用[网络策略][network-policy]。

## <a name="required-outbound-network-rules-and-fqdns-for-aks-clusters"></a>AKS 群集所需的出站网络规则和 FQDN

以下网络和 FQDN/应用程序规则为 AKS 群集所必需，若要配置 Azure 防火墙以外的解决方案，可以使用它们。

* IP 地址依赖项适用于非 HTTP/S 流量（TCP 和 UDP 流量）
* 可将 FQDN HTTP/HTTPS 终结点放在防火墙设备中。
* 通配符 HTTP/HTTPS 终结点是可以根据许多限定符随 AKS 群集一起变化的依赖项。
* AKS 使用准入控制器将 FQDN 作为环境变量注入 kube-system 和 gatekeeper-system下的所有部署，确保节点和 API 服务器之间的所有系统通信使用 API 服务器 FQDN 而不是 API 服务器 IP。 
* 如果有需要与 API 服务器通信的应用或解决方案，则必须添加其他网络规则，以允许与 API 服务器 IP 的端口 443 进行 TCP 通信。
* 在极少数情况下，如果存在维护操作，则 API 服务器 IP 可能更改。 始终会提前传达可以更改 API 服务器 IP 的计划内维护操作。


### <a name="azure-global-required-network-rules"></a>Azure 全球的必需网络规则

必需的网络规则和 IP 地址依赖项如下：

| 目标终结点                                                             | 协议 | 端口    | 用途  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - `AzureCloud.<Region>:1194` <br/> *Or* <br/> [区域 CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - `RegionCIDRs:1194` <br/> *Or* <br/> **`APIServerIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | 用于节点与控制平面之间的隧道安全通信。 |
| **`*:9000`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - `AzureCloud.<Region>:9000` <br/> *Or* <br/> [区域 CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - `RegionCIDRs:9000` <br/> *Or* <br/> **`APIServerIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | 用于节点与控制平面之间的隧道安全通信。 |
| `*:123` 或 `ntp.ubuntu.com:123`（如果使用 Azure 防火墙网络规则）   | UDP      | 123     | 在 Linux 节点上进行网络时间协议 (NTP) 时间同步时需要。                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | 如果使用的是自定义 DNS 服务器，必须确保群集节点可以访问这些服务器。 |
| **`APIServerIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | 运行访问 API 服务器的 Pod/部署时需要，这些 Pod/部署将使用 API IP。  |

### <a name="azure-global-required-fqdn--application-rules"></a>Azure 全球的必需 FQDN/应用程序规则 

必须具有以下 FQDN/应用程序规则：

| 目标 FQDN                 | 端口            | 用途      |
|----------------------------------|-----------------|----------|
| **`*.hcp.<location>.azmk8s.io`** | **`HTTPS:443`** | Node <-> API 服务器通信时需要。 将 \<location\> 替换为部署 AKS 群集的区域。 |
| **`mcr.microsoft.com`**          | **`HTTPS:443`** | 访问 Microsoft 容器注册表 (MCR) 中的映像时需要。 此注册表包含第一方映像/图表（例如 coreDNS 等）。 这些映像是正确创建和正常运行群集所必需的，包括缩放和升级操作。  |
| **`*.cdn.mscr.io`**              | **`HTTPS:443`** | 对于 Azure 内容分发网络 (CDN) 支持的 MCR 存储是必需的。 |
| **`*.data.mcr.microsoft.com`**   | **`HTTPS:443`** | 对于 Azure 内容分发网络 (CDN) 支持的 MCR 存储是必需的。 |
| **`management.azure.com`**       | **`HTTPS:443`** | 对于针对 Azure API 的 Kubernetes 操作是必需的。 |
| **`login.microsoftonline.com`**  | **`HTTPS:443`** | 对于 Azure Active Directory 身份验证是必需的。 |
| **`packages.microsoft.com`**     | **`HTTPS:443`** | Microsoft 包存储库使用此地址缓存 apt-get 操作。  示例包包括 Moby、PowerShell 和 Azure CLI。 |
| **`acs-mirror.azureedge.net`**   | **`HTTPS:443`** | 此地址用于下载和安装所需二进制文件（如 kubenet 和 Azure CNI）所需的存储库。 |

### <a name="azure-china-21vianet-required-network-rules"></a>Azure 中国世纪互联的必需网络规则

必需的网络规则和 IP 地址依赖项如下：

| 目标终结点                                                             | 协议 | 端口    | 用途  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - `AzureCloud.Region:1194` <br/> *Or* <br/> [区域 CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - `RegionCIDRs:1194` <br/> *Or* <br/> **`APIServerIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | 用于节点与控制平面之间的隧道安全通信。 |
| **`*:9000`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - `AzureCloud.<Region>:9000` <br/> *Or* <br/> [区域 CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - `RegionCIDRs:9000` <br/> *Or* <br/> **`APIServerIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | 用于节点与控制平面之间的隧道安全通信。 |
| **`*:22`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - `AzureCloud.<Region>:22` <br/> *Or* <br/> [区域 CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - `RegionCIDRs:22` <br/> *Or* <br/> **`APIServerIP:22`** `(only known after cluster creation)`  | TCP           | 22      | 用于节点与控制平面之间的隧道安全通信。 |
| `*:123` 或 `ntp.ubuntu.com:123`（如果使用 Azure 防火墙网络规则）   | UDP      | 123     | 在 Linux 节点上进行网络时间协议 (NTP) 时间同步时需要。                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | 如果使用的是自定义 DNS 服务器，必须确保群集节点可以访问这些服务器。 |
| **`APIServerIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | 运行访问 API 服务器的 Pod/部署时需要，这些 Pod/部署将使用 API IP。  |

### <a name="azure-china-21vianet-required-fqdn--application-rules"></a>Azure 中国世纪互联的必需 FQDN/应用程序规则

必须具有以下 FQDN/应用程序规则：

| 目标 FQDN                               | 端口            | 用途      |
|------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | Node <-> API 服务器通信时需要。 将 \<location\> 替换为部署 AKS 群集的区域。 |
| **`*.tun.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | Node <-> API 服务器通信时需要。 将 \<location\> 替换为部署 AKS 群集的区域。 |
| **`mcr.microsoft.com`**                        | **`HTTPS:443`** | 访问 Microsoft 容器注册表 (MCR) 中的映像时需要。 此注册表包含第一方映像/图表（例如 coreDNS 等）。 这些映像是正确创建和正常运行群集所必需的，包括缩放和升级操作。 |
| **`*.cdn.mscr.io`**                            | **`HTTPS:443`** | 对于 Azure 内容分发网络 (CDN) 支持的 MCR 存储是必需的。 |
| **`.data.mcr.microsoft.com`**                  | **`HTTPS:443`** | 对于 Azure 内容分发网络 (CDN) 支持的 MCR 存储是必需的。 |
| **`management.chinacloudapi.cn`**              | **`HTTPS:443`** | 对于针对 Azure API 的 Kubernetes 操作是必需的。 |
| **`login.chinacloudapi.cn`**                   | **`HTTPS:443`** | 对于 Azure Active Directory 身份验证是必需的。 |
| **`packages.microsoft.com`**                   | **`HTTPS:443`** | Microsoft 包存储库使用此地址缓存 apt-get 操作。  示例包包括 Moby、PowerShell 和 Azure CLI。 |
| **`*.azk8s.cn`**                               | **`HTTPS:443`** | 此地址用于下载和安装所需二进制文件（如 kubenet 和 Azure CNI）所需的存储库。 |

### <a name="azure-us-government-required-network-rules"></a>Azure 美国政府的必需网络规则

必需的网络规则和 IP 地址依赖项如下：

| 目标终结点                                                             | 协议 | 端口    | 用途  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - `AzureCloud.<Region>:1194` <br/> *Or* <br/> [区域 CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - `RegionCIDRs:1194` <br/> *Or* <br/> **`APIServerIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | 用于节点与控制平面之间的隧道安全通信。 |
| **`*:9000`** <br/> *Or* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - `AzureCloud.<Region>:9000` <br/> *Or* <br/> [区域 CIDR](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - `RegionCIDRs:9000` <br/> *Or* <br/> **`APIServerIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | 用于节点与控制平面之间的隧道安全通信。 |
| `*:123` 或 `ntp.ubuntu.com:123`（如果使用 Azure 防火墙网络规则）   | UDP      | 123     | 在 Linux 节点上进行网络时间协议 (NTP) 时间同步时需要。                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | 如果使用的是自定义 DNS 服务器，必须确保群集节点可以访问这些服务器。 |
| **`APIServerIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | 运行访问 API 服务器的 Pod/部署时需要，这些 Pod/部署将使用 API IP。  |

### <a name="azure-us-government-required-fqdn--application-rules"></a>Azure 美国政府的必需 FQDN/应用程序规则 

必须具有以下 FQDN/应用程序规则：

| 目标 FQDN                                        | 端口            | 用途      |
|---------------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.aks.containerservice.azure.us`** | **`HTTPS:443`** | Node <-> API 服务器通信时需要。 将 \<location\> 替换为部署 AKS 群集的区域。|
| **`mcr.microsoft.com`**                                 | **`HTTPS:443`** | 访问 Microsoft 容器注册表 (MCR) 中的映像时需要。 此注册表包含第一方映像/图表（例如 coreDNS 等）。 这些映像是正确创建和正常运行群集所必需的，包括缩放和升级操作。 |
| **`*.cdn.mscr.io`**                                     | **`HTTPS:443`** | 对于 Azure 内容分发网络 (CDN) 支持的 MCR 存储是必需的。 |
| **`*.data.mcr.microsoft.com`**                          | **`HTTPS:443`** | 对于 Azure 内容分发网络 (CDN) 支持的 MCR 存储是必需的。 |
| **`management.usgovcloudapi.net`**                      | **`HTTPS:443`** | 对于针对 Azure API 的 Kubernetes 操作是必需的。 |
| **`login.microsoftonline.us`**                          | **`HTTPS:443`** | 对于 Azure Active Directory 身份验证是必需的。 |
| **`packages.microsoft.com`**                            | **`HTTPS:443`** | Microsoft 包存储库使用此地址缓存 apt-get 操作。  示例包包括 Moby、PowerShell 和 Azure CLI。 |
| **`acs-mirror.azureedge.net`**                          | **`HTTPS:443`** | 此地址用于安装所需二进制文件（如 kubenet 和 Azure CNI）所需的存储库。 |

## <a name="optional-recommended-fqdn--application-rules-for-aks-clusters"></a>适用于 AKS 群集的可选的推荐 FQDN/应用程序规则

以下 FQDN/应用程序规则是可选的，但建议为 AKS 群集使用：

| 目标 FQDN                                                               | 端口          | 用途      |
|--------------------------------------------------------------------------------|---------------|----------|
| **`security.ubuntu.com`、`azure.archive.ubuntu.com`、`changelogs.ubuntu.com`** | **`HTTP:80`** | 此地址允许 Linux 群集节点下载必需的安全修补程序和更新。 |

如果选择阻止/不允许这些 Fqdn，则节点将仅在你进行 [节点映像升级](node-image-upgrade.md) 或 [群集升级](upgrade-cluster.md)时接收 OS 更新。

## <a name="gpu-enabled-aks-clusters"></a>启用 GPU 的 AKS 群集

### <a name="required-fqdn--application-rules"></a>必需的 FQDN/应用程序规则

启用 GPU 的 AKS 群集需要以下 FQDN/应用程序规则：

| 目标 FQDN                        | 端口      | 用途      |
|-----------------------------------------|-----------|----------|
| **`nvidia.github.io`**                  | **`HTTPS:443`** | 此地址用于在基于 GPU 的节点上进行正确的驱动程序安装和操作。 |
| **`us.download.nvidia.com`**            | **`HTTPS:443`** | 此地址用于在基于 GPU 的节点上进行正确的驱动程序安装和操作。 |
| **`apt.dockerproject.org`**             | **`HTTPS:443`** | 此地址用于在基于 GPU 的节点上进行正确的驱动程序安装和操作。 |

## <a name="windows-server-based-node-pools"></a>基于 Windows Server 的节点池 

### <a name="required-fqdn--application-rules"></a>必需的 FQDN/应用程序规则

使用基于 Windows Server 的节点池需要以下 FQDN/应用程序规则：

| 目标 FQDN                                                           | 端口      | 用途      |
|----------------------------------------------------------------------------|-----------|----------|
| **`onegetcdn.azureedge.net, go.microsoft.com`**                            | **`HTTPS:443`** | 安装与 windows 相关的二进制文件 |
| **`*.mp.microsoft.com, www.msftconnecttest.com, ctldl.windowsupdate.com`** | **`HTTP:80`**   | 安装与 windows 相关的二进制文件 |

## <a name="aks-addons-and-integrations"></a>AKS 加载项和集成

### <a name="azure-monitor-for-containers"></a>用于容器的 Azure Monitor

有两个选项可以提供对用于容器的 Azure Monitor 的访问，你可以允许 Azure Monitor [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags)或者提供对必需 FQDN/应用程序规则的访问权限。

#### <a name="required-network-rules"></a>必需的网络规则

必须具有以下 FQDN/应用程序规则：

| 目标终结点                                                             | 协议 | 端口    | 用途  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - `AzureMonitor:443`  | TCP           | 443      | 此终结点用于将指标数据和日志发送到 Azure Monitor 和 Log Analytics。 |

#### <a name="required-fqdn--application-rules"></a>必需的 FQDN/应用程序规则

启用了用于容器的 Azure Monitor 的 AKS 群集需要以下 FQDN/应用程序规则：

| FQDN                                    | 端口      | 用途      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | **`HTTPS:443`**    | 此终结点适用于使用 Azure Monitor 的指标和监视遥测。 |
| *.ods.opinsights.azure.com    | **`HTTPS:443`**    | Azure Monitor 使用此终结点来引入日志分析数据。 |
| *.oms.opinsights.azure.com | **`HTTPS:443`** | 此终结点由 omsagent 使用，用于对日志分析服务进行身份验证。 |
| *.monitoring.azure.com | **`HTTPS:443`** | 此终结点用于将指标数据发送到 Azure Monitor。 |

### <a name="azure-dev-spaces"></a>Azure Dev Spaces

更新防火墙或安全配置，以允许与以下所有 Fqdn 和 [Azure Dev Spaces 基础结构服务][dev-spaces-service-tags]的网络流量。

#### <a name="required-network-rules"></a>必需的网络规则

| 目标终结点                                                             | 协议 | 端口    | 用途  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - `AzureDevSpaces`  | TCP           | 443      | 此终结点用于将指标数据和日志发送到 Azure Monitor 和 Log Analytics。 |

#### <a name="required-fqdn--application-rules"></a>必需的 FQDN/应用程序规则 

启用了 Azure Dev Spaces 的 AKS 群集需要以下 FQDN/应用程序规则：

| FQDN                                    | 端口      | 用途      |
|-----------------------------------------|-----------|----------|
| `cloudflare.docker.com` | **`HTTPS:443`** | 此地址用于请求 linux alpine 和其他 Azure Dev Spaces 映像 |
| `gcr.io` | **`HTTPS:443`** | 此地址用于请求 helm/tiller 映像 |
| `storage.googleapis.com` | **`HTTPS:443`** | 此地址用于请求 helm/tiller 映像 |


### <a name="azure-policy-preview"></a>Azure Policy（预览）

> [!CAUTION]
> 下面的某些功能处于预览阶段。  随着此功能向公共预览版和未来发布阶段发展，本文中的建议可能会有所不同。

#### <a name="required-fqdn--application-rules"></a>必需的 FQDN/应用程序规则 

启用了 Azure Policy 的 AKS 群集需要以下 FQDN/应用程序规则。

| FQDN                                          | 端口      | 用途      |
|-----------------------------------------------|-----------|----------|
| **`gov-prod-policy-data.trafficmanager.net`** | **`HTTPS:443`** | 此地址用于正确操作 Azure Policy。 （AKS 中目前为预览版） |
| **`raw.githubusercontent.com`**               | **`HTTPS:443`** | 此地址用于从 GitHub 请求内置策略，以确保正确操作 Azure Policy。 （AKS 中目前为预览版） |
| **`dc.services.visualstudio.com`**            | **`HTTPS:443`** | Azure Policy 加载项，用于向应用程序见解终结点发送遥测数据。 |


## <a name="restrict-egress-traffic-using-azure-firewall"></a>使用 Azure 防火墙限制出口流量

Azure 防火墙提供 Azure Kubernetes 服务 (`AzureKubernetesService`) FQDN 标记以简化此配置。 

> [!NOTE]
> FQDN 标记包含上面列出的所有 FQDN，并自动保持最新。
>
> 对于生产方案，我们建议在 Azure 防火墙上至少具有20个前端 Ip，以避免出现 SNAT 端口耗尽问题。

下面是部署的示例体系结构：

![锁定的拓扑](media/limit-egress-traffic/aks-azure-firewall-egress.png)

* 强制公共入口流量流经防火墙筛选器
  * AKS 代理节点隔离在专用子网中。
  * [Azure 防火墙](../firewall/overview.md)部署在自己的子网中。
  * DNAT 规则将 FW 公共 IP 转换为 LB 前端 IP。
* 出站请求从代理节点启动并使用[用户定义的路由](egress-outboundtype.md)发送到 Azure 防火墙内部 IP
  * 来自 AKS 代理节点的请求遵循 AKS 群集所部署到的子网中已放置的 UDR。
  * Azure 防火墙通过公共 IP 前端将流量传出虚拟网络
  * 对公共 Internet 或其他 Azure 服务的访问流量会流入和流出防火墙前端 IP 地址
  * 根据需要，[API 服务器授权的 IP 范围](./api-server-authorized-ip-ranges.md)（包括防火墙公共前端 IP 地址）保护对 AKS 控制平面的访问。
* 内部流量
  * 根据需要，除了[公共负载均衡器](load-balancer-standard.md)外，可以（改为）将[内部负载均衡器](internal-lb.md)用于内部流量，也可以在自身的子网中隔离它。


以下步骤使用 Azure 防火墙的 `AzureKubernetesService` FQDN 标记限制来自 AKS 群集的出站流量，并提供如何通过防火墙配置公用入站流量的示例。


### <a name="set-configuration-via-environment-variables"></a>通过环境变量设置配置

定义创建资源时要使用的一组环境变量。

```bash
PREFIX="aks-egress"
RG="${PREFIX}-rg"
LOC="eastus"
PLUGIN=azure
AKSNAME="${PREFIX}"
VNET_NAME="${PREFIX}-vnet"
AKSSUBNET_NAME="aks-subnet"
# DO NOT CHANGE FWSUBNET_NAME - This is currently a requirement for Azure Firewall.
FWSUBNET_NAME="AzureFirewallSubnet"
FWNAME="${PREFIX}-fw"
FWPUBLICIP_NAME="${PREFIX}-fwpublicip"
FWIPCONFIG_NAME="${PREFIX}-fwconfig"
FWROUTE_TABLE_NAME="${PREFIX}-fwrt"
FWROUTE_NAME="${PREFIX}-fwrn"
FWROUTE_NAME_INTERNET="${PREFIX}-fwinternet"
```

### <a name="create-a-virtual-network-with-multiple-subnets"></a>创建包含多个子网的虚拟网络

预配包含两个单独子网的虚拟网络，其中一个子网用于群集，一个子网用于防火墙。 还可以选择为内部服务入口创建一个。

![空网络拓扑](media/limit-egress-traffic/empty-network.png)

创建一个资源组来存放所有资源。

```azurecli
# Create Resource Group

az group create --name $RG --location $LOC
```

创建具有两个子网的虚拟网络来托管 AKS 群集和 Azure 防火墙。 每个虚拟网络都具有自己的子网。 让我们从 AKS 网络开始。

```
# Dedicated virtual network with AKS subnet

az network vnet create \
    --resource-group $RG \
    --name $VNET_NAME \
    --location $LOC \
    --address-prefixes 10.42.0.0/16 \
    --subnet-name $AKSSUBNET_NAME \
    --subnet-prefix 10.42.1.0/24

# Dedicated subnet for Azure Firewall (Firewall name cannot be changed)

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $FWSUBNET_NAME \
    --address-prefix 10.42.2.0/24
```

### <a name="create-and-set-up-an-azure-firewall-with-a-udr"></a>使用 UDR 来创建和设置 Azure 防火墙

必须配置 Azure 防火墙入站和出站规则。 防火墙的主要用途是使组织能够针对传入和传出 AKS 群集的流量配置精细的规则。

![防火墙和 UDR](media/limit-egress-traffic/firewall-udr.png)


> [!IMPORTANT]
> 如果群集或应用程序创建众多定向到相同目标或目标子集的出站连接，则可能需要更多的防火墙前端 IP 来避免用尽每个前端 IP 的端口。
> 有关如何创建具有多个 IP 的 Azure 防火墙的详细信息，请参阅[此处](../firewall/quick-create-multiple-ip-template.md)

创建将用作 Azure 防火墙前端地址的标准 SKU 公共 IP 资源。

```azurecli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

注册预览版 CLI 扩展以创建 Azure 防火墙。
```azurecli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC --enable-dns-proxy true
```
现在，可将前面创建的 IP 地址分配到防火墙前端。

> [!NOTE]
> 设置 Azure 防火墙的公共 IP 地址可能需要几分钟时间。
> 若要对网络规则使用 FQDN，需要启用 DNS 代理。如果启用，防火墙将侦听端口 53，并将 DNS 请求转发到上面指定的 DNS 服务器。 这将允许防火墙自动转换该 FQDN。

```azurecli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

当前面的命令成功时，保存防火墙前端 IP 地址以便稍后进行配置。

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

> [!NOTE]
> 如果通过[授权 IP 地址范围](./api-server-authorized-ip-ranges.md)安全访问 AKS API 服务器，需要将防火墙公共 IP 添加到授权的 IP 范围。

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>创建包含 Azure 防火墙跃点的 UDR

Azure 自动在 Azure 子网、虚拟网络与本地网络之间路由流量。 若要更改 Azure 的任何默认路由，可以创建一个路由表。

创建一个要与给定子网关联的空路由表。 该路由表将下一跃点定义为前面创建的 Azure 防火墙。 每个子网可以有一个与之关联的路由表，也可以没有。

```azurecli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG -l $LOC --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

请参阅[虚拟网络路由表文档](../virtual-network/virtual-networks-udr-overview.md#user-defined)，了解如何替代 Azure 的默认系统路由或者在子网的路由表中添加更多路由。

### <a name="adding-firewall-rules"></a>添加防火墙规则

下面是可用于在防火墙上配置的三个网络规则，可能需要根据部署来调整这些规则。 第一个规则允许通过 TCP 访问端口 9000。 第二个规则允许通过 UDP 访问端口 1194 和 123（如果部署到 Azure 中国世纪互联，可能需要[更多](#azure-china-21vianet-required-network-rules)）。 这两个规则将仅允许目标为当前使用的 Azure 区域 CIDR 的流量，在本例中为 "美国东部"。 最后添加第三个网络规则，通过 UDP 打开端口 123 转到 `ntp.ubuntu.com` FQDN（将 FQDN 添加为网络规则是 Azure 防火墙的一项特定功能，使用自己的选项时需要对其进行调整）。

设置网络规则后，还将使用 `AzureKubernetesService` 添加应用程序规则，该规则涵盖可通过 TCP 端口 443 和端口 80 访问的所有必需 FQDN。

```
# Add FW Network Rules

az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apiudp' --protocols 'UDP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 1194 --action allow --priority 100
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apitcp' --protocols 'TCP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 9000
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'time' --protocols 'UDP' --source-addresses '*' --destination-fqdns 'ntp.ubuntu.com' --destination-ports 123

# Add FW Application Rules

az network firewall application-rule create -g $RG -f $FWNAME --collection-name 'aksfwar' -n 'fqdn' --source-addresses '*' --protocols 'http=80' 'https=443' --fqdn-tags "AzureKubernetesService" --action allow --priority 100
```

请参阅 [Azure 防火墙文档](../firewall/overview.md)来详细了解 Azure 防火墙服务。

### <a name="associate-the-route-table-to-aks"></a>将路由表关联到 AKS

若要将群集与防火墙相关联，群集子网的专用子网必须引用前面创建的路由表。 可以通过向包含群集和防火墙的虚拟网络发出更新群集子网路由表的命令来执行关联。

```azurecli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

### <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>将出站类型为 UDR 的 AKS 部署到现有网络

现在，可将 AKS 群集部署到现有的虚拟网络。 还将使用[出站类型`userDefinedRouting`](egress-outboundtype.md)，此功能确保通过防火墙强制执行任何出站流量，并且不存在其他传出路径（默认情况下，可以使用负载均衡器出站类型）。

![aks-deploy](media/limit-egress-traffic/aks-udr-fw.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>创建有权在现有虚拟网络中进行预配的服务主体

AKS 使用服务主体来创建群集资源。 创建时传递的服务主体用于创建 AKS 所使用的基本 AKS 资源（例如存储资源、Ip 和负载均衡器） (你也可以改为使用 [托管标识](use-managed-identity.md)) 。 如果未授予以下适当的权限，则无法预配 AKS 群集。

```azurecli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

现在，请将下面的 `APPID` 和 `PASSWORD` 替换为前一命令输出自动生成的服务主体 appid 和服务主体密码。 将引用 VNET 资源 ID 来向服务主体授予权限，使 AKS 能够将资源部署到其中。

```azurecli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role "Network Contributor"
```

可以在[此处](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)查看所需的详细权限。

> [!NOTE]
> 如果使用的是 kubenet 网络插件，则需要为预先创建的路由表提供 AKS 服务主体或托管标识权限，因为 kubenet 需要路由表来添加必要传递规则。 
> ```azurecli-interactive
> RTID=$(az network route-table show -g $RG -n $FWROUTE_TABLE_NAME --query id -o tsv)
> az role assignment create --assignee $APPID --scope $RTID --role "Network Contributor"
> ```

### <a name="deploy-aks"></a>部署 AKS

最后，可将 AKS 群集部署到专用于群集的现有子网中。 要部署到的目标子网是使用环境变量 (`$SUBNETID`) 定义的。 在前面的步骤中，我们未定义 `$SUBNETID` 变量。 若要设置子网 ID 的值，可使用以下命令：

```azurecli
SUBNETID=$(az network vnet subnet show -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --query id -o tsv)
```

使用子网中已存在的 UDR 来定义出站类型。 此配置使 AKS 跳过负载均衡器的设置和 IP 预配。

> [!IMPORTANT]
> 有关出站类型 UDR（包括限制）的详细信息，请参阅[流出量出站类型 UDR](egress-outboundtype.md#limitations)。


> [!TIP]
> 可以向群集部署（如 [**专用群集**](private-clusters.md)）添加其他功能。 
>
> 可以添加 [API 服务器已授权 IP 范围](api-server-authorized-ip-ranges.md) AKS 功能，以便限制 API 服务器仅访问防火墙的公共终结点。 已授权 IP 范围功能在图中表示为可选。 启用已授权 IP 范围功能来限制 API 服务器访问权限时，开发人员工具必须使用防火墙虚拟网络中的 Jumpbox，或者必须将所有开发人员终结点添加到已授权 IP 范围。

```azurecli
az aks create -g $RG -n $AKSNAME -l $LOC \
  --node-count 3 --generate-ssh-keys \
  --network-plugin $PLUGIN \
  --outbound-type userDefinedRouting \
  --service-cidr 10.41.0.0/16 \
  --dns-service-ip 10.41.0.10 \
  --docker-bridge-address 172.17.0.1/16 \
  --vnet-subnet-id $SUBNETID \
  --service-principal $APPID \
  --client-secret $PASSWORD \
  --api-server-authorized-ip-ranges $FWPUBLIC_IP
```

### <a name="enable-developer-access-to-the-api-server"></a>使开发人员能够访问 API 服务器

如果在上一步中为群集使用了已授权 IP 范围，则必须将开发人员工具 IP 地址添加到 AKS 群集的已批准 IP 范围列表，以便从该处访问 API 服务器。 另一种做法是在防火墙虚拟网络中的单独子网内，使用所需的工具配置 Jumpbox。

使用以下命令将另一个 IP 地址添加到已批准范围

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 使用 [az aks get-credentials][az-aks-get-credentials] 命令将 `kubectl` 配置为连接到新建的 Kubernetes 群集。 

 ```azurecli
 az aks get-credentials -g $RG -n $AKSNAME
 ```

### <a name="deploy-a-public-service"></a>部署公共服务
现在可以开始公开服务并将应用程序部署到此群集。 此示例将公开公共服务，但也可以选择通过[内部负载均衡器](internal-lb.md)公开内部服务。

![公共服务 DNAT](media/limit-egress-traffic/aks-create-svc.png)

通过将以下 yaml 复制为名为 `example.yaml` 的文件来部署 Azure 投票应用程序。

```yaml
# voting-storage-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-storage
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-storage
  template:
    metadata:
      labels:
        app: voting-storage
    spec:
      containers:
      - name: voting-storage
        image: mcr.microsoft.com/aks/samples/voting/storage:2.0
        args: ["--ignore-db-dir=lost+found"]
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 3306
          name: mysql
        volumeMounts:
        - name: mysql-persistent-storage
          mountPath: /var/lib/mysql
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_ROOT_PASSWORD
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
      volumes:
      - name: mysql-persistent-storage
        persistentVolumeClaim:
          claimName: mysql-pv-claim
---
# voting-storage-secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: voting-storage-secret
type: Opaque
data:
  MYSQL_USER: ZGJ1c2Vy
  MYSQL_PASSWORD: UGFzc3dvcmQxMg==
  MYSQL_DATABASE: YXp1cmV2b3Rl
  MYSQL_ROOT_PASSWORD: UGFzc3dvcmQxMg==
---
# voting-storage-pv-claim.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
---
# voting-storage-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-storage
  labels: 
    app: voting-storage
spec:
  ports:
  - port: 3306
    name: mysql
  selector:
    app: voting-storage
---
# voting-app-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-app
  template:
    metadata:
      labels:
        app: voting-app
    spec:
      containers:
      - name: voting-app
        image: mcr.microsoft.com/aks/samples/voting/app:2.0
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
          name: http
        env:
        - name: MYSQL_HOST
          value: "voting-storage"
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
        - name: ANALYTICS_HOST
          value: "voting-analytics"
---
# voting-app-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-app
  labels: 
    app: voting-app
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 8080
    name: http
  selector:
    app: voting-app
---
# voting-analytics-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-analytics
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-analytics
      version: "2.0"
  template:
    metadata:
      labels:
        app: voting-analytics
        version: "2.0"
    spec:
      containers:
      - name: voting-analytics
        image: mcr.microsoft.com/aks/samples/voting/analytics:2.0
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
          name: http
        env:
        - name: MYSQL_HOST
          value: "voting-storage"
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
---
# voting-analytics-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-analytics
  labels: 
    app: voting-analytics
spec:
  ports:
  - port: 8080
    name: http
  selector:
    app: voting-analytics
```

运行以下命令来部署服务：

```bash
kubectl apply -f example.yaml
```

### <a name="add-a-dnat-rule-to-azure-firewall"></a>将 DNAT 规则添加到 Azure 防火墙

> [!IMPORTANT]
> 使用 Azure 防火墙限制出口流量并创建用户定义的路由 (UDR) 来强制所有出口流量时，请确保在防火墙中创建适当的 DNAT 规则，以正确允许入口流量。 结合使用 Azure 防火墙和 UDR 时，会因为路由不对称而中断入口设置。 （如果 AKS 子网具有指向防火墙专用 IP 地址的默认路由，但你使用的是公共负载均衡器 - 类型为 LoadBalancer 的入口或 Kubernetes 服务，则会出现此问题）。 在这种情况下，将通过负载均衡器的公共 IP 地址接收传入的负载均衡器流量，但返回路径将通过防火墙的专用 IP 地址。 由于防火墙是有状态的，并且无法识别已建立的会话，因此会丢弃返回的数据包。 若要了解如何将 Azure 防火墙与入口或服务负载均衡器集成，请参阅[将 Azure 防火墙与 Azure 标准负载均衡器集成](../firewall/integrate-lb.md)。


若要配置入站连接，必须将一个 DNAT 规则写入到 Azure 防火墙。 为了测试与群集的连接，为防火墙前端公共 IP 地址定义了规则，以便路由到内部服务公开的内部 IP。

可以自定义目标地址，因为它是防火墙上要访问的端口。 转换的地址必须是内部负载均衡器的 IP 地址。 转换的端口必须是 Kubernetes 服务的已公开端口。

需要指定分配给 Kubernetes 服务所创建的负载均衡器的内部 IP 地址。 运行以下命令来检索该地址：

```bash
kubectl get services
```

所需的 IP 地址将在“EXTERNAL-IP”列中列出，如下所示。

```bash
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes         ClusterIP      10.41.0.1       <none>        443/TCP        10h
voting-analytics   ClusterIP      10.41.88.129    <none>        8080/TCP       9m
voting-app         LoadBalancer   10.41.185.82    20.39.18.6    80:32718/TCP   9m
voting-storage     ClusterIP      10.41.221.201   <none>        3306/TCP       9m
```

运行以下内容来获取服务 IP：
```bash
SERVICE_IP=$(k get svc voting-app -o jsonpath='{.status.loadBalancer.ingress[*].ip}')
```

运行以下内容来添加 NAT 规则：
```azurecli
az network firewall nat-rule create --collection-name exampleset --destination-addresses $FWPUBLIC_IP --destination-ports 80 --firewall-name $FWNAME --name inboundrule --protocols Any --resource-group $RG --source-addresses '*' --translated-port 80 --action Dnat --priority 100 --translated-address $SERVICE_IP
```

### <a name="validate-connectivity"></a>验证连接

在浏览器中导航到 Azure 防火墙前端 IP 地址来验证连接。

应看到 AKS 投票应用程序。 在此示例中，防火墙公共 IP 为 `52.253.228.132` 。


![aks-vote](media/limit-egress-traffic/aks-vote.png)


### <a name="clean-up-resources"></a>清理资源

若要清理 Azure 资源，请删除 AKS 资源组。

```azurecli
az group delete -g $RG
```

## <a name="next-steps"></a>后续步骤

通过学习这篇文章，了解了要限制群集的出口流量时可以使用的端口和地址。 还知道了如何使用 Azure 防火墙保护出站流量。 

如果需要，可以将以上步骤推广到将流量转发到首选出口解决方案，根据[出站类型`userDefinedRoute`文档](egress-outboundtype.md)进行操作。

如果想要限制 Pod 在彼此之间如何通信以及群集内的东-西流量限制，请参阅[在 AKS 中使用网络策略保护 Pod 之间的流量][network-policy]。

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
[dev-spaces-service-tags]: ../dev-spaces/configure-networking.md#virtual-network-or-subnet-configurations
