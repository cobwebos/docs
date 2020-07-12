---
title: 限制 Azure Kubernetes 服务 (AKS) 中的出口流量
description: 了解控制 Azure Kubernetes 服务 (AKS) 中的出口流量所需的端口和地址
services: container-service
ms.topic: article
ms.author: jpalma
ms.date: 06/29/2020
author: palma21
ms.openlocfilehash: 9d06852e9d3d61b3e3d368a1d1c6f4107aff1442
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2020
ms.locfileid: "86251308"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>控制 Azure Kubernetes 服务 (AKS) 中群集节点的出口流量

本文提供了一些必要的详细信息，可用于保护来自 Azure Kubernetes 服务的出站流量 (AKS) 。 它包含基本 AKS 部署的群集要求，以及可选的加载项和功能的其他要求。 [最后提供了一个示例，介绍如何使用 Azure 防火墙配置这些要求](#restrict-egress-traffic-using-azure-firewall)。 但是，你可以将此信息应用到任何出站限制方法或设备。

## <a name="background"></a>背景

AKS 群集部署在虚拟网络上。 此网络可以由 AKS)  (创建，也可以由用户提前) 预先配置的自定义 (进行管理。 在这两种情况下，群集都具有与该虚拟网络外部的服务的**出站**依赖关系 (该服务没有) 的入站依赖项。

出于管理和操作目的，AKS 群集中的节点需要访问某些端口和完全限定的域名 (FQDN)。 这些终结点是节点与 API 服务器进行通信所必需的，或者是为了下载和安装 core Kubernetes 群集组件和节点安全更新所必需的。 例如，群集需要从 Microsoft 容器注册表 (MCR) 提取基本系统容器映像。

AKS 出站依赖关系几乎完全使用 Fqdn 定义，后者后面没有静态地址。 缺少静态地址意味着不能使用网络安全组来锁定 AKS 群集的出站流量。 

默认情况下，AKS 群集具有不受限制的出站（出口）internet 访问权限。 此级别的网络访问权限允许运行的节点和服务根据需要访问外部资源。 如果要限制出口流量，则必须访问有限数量的端口和地址才能维护正常的群集维护任务。 保护出站地址的最简单的解决方案是使用防火墙设备，该设备可以基于域名控制出站流量。 例如，Azure 防火墙可以基于目标的 FQDN 限制出站 HTTP 和 HTTPS 流量。 你还可以配置你的首选防火墙和安全规则，以允许这些必需的端口和地址。

> [!IMPORTANT]
> 本文档仅介绍如何锁定离开 AKS 子网的流量。 默认情况下，AKS 没有入口要求。  不支持使用网络安全组 (Nsg) 和防火墙阻止**内部子网流量**。 若要控制和阻止群集中的流量，请使用[***网络策略***][network-policy]。

## <a name="required-outbound-network-rules-and-fqdns-for-aks-clusters"></a>AKS 群集所需的出站网络规则和 Fqdn

若要配置 Azure 防火墙以外的解决方案，可以使用 AKS 群集所需的以下网络和 FQDN/应用程序规则。

* IP 地址依赖项适用于非 HTTP/S 流量（TCP 和 UDP 流量）
* 可将 FQDN HTTP/HTTPS 终结点放在防火墙设备中。
* 通配符 HTTP/HTTPS 终结点是依赖项，这些依赖项可能会根据多个限定符在 AKS 群集上发生变化。
* AKS 使用一个许可控制器将 FQDN 作为环境变量注入到 kube 系统和网关守卫系统下的所有部署，这可确保节点和 API 服务器之间的所有系统通信使用 API 服务器 FQDN 而不是 API 服务器 IP。 
* 如果有需要与 API 服务器通信的应用或解决方案，则必须添加**其他**网络规则，以允许*与 API 服务器的 IP 的端口443进行 TCP 通信*。
* 在极少数情况下，如果有维护操作，则 API 服务器 IP 可能会更改。 始终会提前传达可以更改 API 服务器 IP 的计划内维护操作。


### <a name="azure-global-required-network-rules"></a>Azure 全局要求的网络规则

所需的网络规则和 IP 地址依赖关系如下：

| 目标终结点                                                             | 协议 | 端口    | 用途  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *或* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *或* <br/> [区域 CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *或* <br/> **`APIServerIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | 用于节点与控制平面之间的隧道安全通信。 |
| **`*:9000`** <br/> *或* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *或* <br/> [区域 CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *或* <br/> **`APIServerIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | 用于节点与控制平面之间的隧道安全通信。 |
| **`*:123`****`ntp.ubuntu.com:123`** 如果使用 Azure 防火墙网络规则，则为或 ()   | UDP      | 123     | 网络时间协议要求 (NTP) Linux 节点上的时间同步。                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | 如果使用的是自定义 DNS 服务器，则必须确保群集节点可以访问这些服务器。 |
| **`APIServerIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | 如果正在运行访问 API 服务器的 pod/部署，则需要使用 API IP。  |

### <a name="azure-global-required-fqdn--application-rules"></a>Azure 全局要求的 FQDN/应用程序规则 

需要以下 FQDN/应用程序规则：

| 目标 FQDN                 | 端口            | 用途      |
|----------------------------------|-----------------|----------|
| **`*.hcp.<location>.azmk8s.io`** | **`HTTPS:443`** | 节点 < > API 服务器通信需要此项。 将替换 *\<location\>* 为 AKS 群集部署到的区域。 |
| **`mcr.microsoft.com`**          | **`HTTPS:443`** | 需要访问 Microsoft 容器注册表 (MCR) 中的图像。 此注册表包含第一方映像/图表 (例如 coreDNS 等 ) 。 这些映像是正确创建和正常运行群集所必需的，包括缩放和升级操作。  |
| **`*.cdn.mscr.io`**              | **`HTTPS:443`** |  (CDN) 支持 Azure 内容分发网络所需的 MCR 存储。 |
| **`*.data.mcr.microsoft.com`**   | **`HTTPS:443`** |  (CDN) 支持 Azure 内容分发网络所需的 MCR 存储。 |
| **`management.azure.com`**       | **`HTTPS:443`** | 对于 Azure API Kubernetes 操作是必需的。 |
| **`login.microsoftonline.com`**  | **`HTTPS:443`** | Azure Active Directory 身份验证所必需的。 |
| **`packages.microsoft.com`**     | **`HTTPS:443`** | 此地址是用于缓存 apt-get 操作的 Microsoft 包存储库。  示例包包括 Moby、PowerShell 和 Azure CLI。 |
| **`acs-mirror.azureedge.net`**   | **`HTTPS:443`** | 此地址适用于下载和安装所需的二进制文件（如 kubenet 和 Azure CNI）所需的存储库。 |

### <a name="azure-china-21vianet-required-network-rules"></a>Azure 中国世纪互联要求网络规则

所需的网络规则和 IP 地址依赖关系如下：

| 目标终结点                                                             | 协议 | 端口    | 用途  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *或* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.Region:1194`** <br/> *或* <br/> [区域 CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *或* <br/> **`APIServerIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | 用于节点与控制平面之间的隧道安全通信。 |
| **`*:9000`** <br/> *或* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *或* <br/> [区域 CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *或* <br/> **`APIServerIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | 用于节点与控制平面之间的隧道安全通信。 |
| **`*:22`** <br/> *或* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:22`** <br/> *或* <br/> [区域 CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:22`** <br/> *或* <br/> **`APIServerIP:22`** `(only known after cluster creation)`  | TCP           | 22      | 用于节点与控制平面之间的隧道安全通信。 |
| **`*:123`****`ntp.ubuntu.com:123`** 如果使用 Azure 防火墙网络规则，则为或 ()   | UDP      | 123     | 网络时间协议要求 (NTP) Linux 节点上的时间同步。                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | 如果使用的是自定义 DNS 服务器，则必须确保群集节点可以访问这些服务器。 |
| **`APIServerIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | 如果运行用于访问 API 服务器的 pod/部署，则需要使用 API IP。  |

### <a name="azure-china-21vianet-required-fqdn--application-rules"></a>Azure 中国世纪互联要求的 FQDN/应用程序规则

需要以下 FQDN/应用程序规则：

| 目标 FQDN                               | 端口            | 用途      |
|------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | 节点 < > API 服务器通信需要此项。 将替换 *\<location\>* 为 AKS 群集部署到的区域。 |
| **`*.tun.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | 节点 < > API 服务器通信需要此项。 将替换 *\<location\>* 为 AKS 群集部署到的区域。 |
| **`mcr.microsoft.com`**                        | **`HTTPS:443`** | 需要访问 Microsoft 容器注册表 (MCR) 中的图像。 此注册表包含第一方映像/图表 (例如 coreDNS 等 ) 。 这些映像是正确创建和正常运行群集所必需的，包括缩放和升级操作。 |
| **`*.cdn.mscr.io`**                            | **`HTTPS:443`** |  (CDN) 支持 Azure 内容分发网络所需的 MCR 存储。 |
| **`.data.mcr.microsoft.com`**                  | **`HTTPS:443`** |  (CDN) 支持 Azure 内容分发网络所需的 MCR 存储。 |
| **`management.chinacloudapi.cn`**              | **`HTTPS:443`** | 对于 Azure API Kubernetes 操作是必需的。 |
| **`login.chinacloudapi.cn`**                   | **`HTTPS:443`** | Azure Active Directory 身份验证所必需的。 |
| **`packages.microsoft.com`**                   | **`HTTPS:443`** | 此地址是用于缓存 apt-get 操作的 Microsoft 包存储库。  示例包包括 Moby、PowerShell 和 Azure CLI。 |
| **`*.azk8s.cn`**                               | **`HTTPS:443`** | 此地址适用于下载和安装所需的二进制文件（如 kubenet 和 Azure CNI）所需的存储库。 |

### <a name="azure-us-government-required-network-rules"></a>Azure 美国政府版要求的网络规则

所需的网络规则和 IP 地址依赖关系如下：

| 目标终结点                                                             | 协议 | 端口    | 用途  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *或* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *或* <br/> [区域 CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *或* <br/> **`APIServerIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | 用于节点与控制平面之间的隧道安全通信。 |
| **`*:9000`** <br/> *或* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *或* <br/> [区域 CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *或* <br/> **`APIServerIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | 用于节点与控制平面之间的隧道安全通信。 |
| **`*:123`****`ntp.ubuntu.com:123`** 如果使用 Azure 防火墙网络规则，则为或 ()   | UDP      | 123     | 网络时间协议要求 (NTP) Linux 节点上的时间同步。                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | 如果使用的是自定义 DNS 服务器，则必须确保群集节点可以访问这些服务器。 |
| **`APIServerIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | 如果正在运行访问 API 服务器的 pod/部署，则需要使用 API IP。  |

### <a name="azure-us-government-required-fqdn--application-rules"></a>Azure 美国政府版要求 FQDN/应用程序规则 

需要以下 FQDN/应用程序规则：

| 目标 FQDN                                        | 端口            | 用途      |
|---------------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.aks.containerservice.azure.us`** | **`HTTPS:443`** | 节点 < > API 服务器通信需要此项。 将替换 *\<location\>* 为 AKS 群集部署到的区域。|
| **`mcr.microsoft.com`**                                 | **`HTTPS:443`** | 需要访问 Microsoft 容器注册表 (MCR) 中的图像。 此注册表包含第一方映像/图表 (例如 coreDNS 等 ) 。 这些映像是正确创建和正常运行群集所必需的，包括缩放和升级操作。 |
| **`*.cdn.mscr.io`**                                     | **`HTTPS:443`** |  (CDN) 支持 Azure 内容分发网络所需的 MCR 存储。 |
| **`*.data.mcr.microsoft.com`**                          | **`HTTPS:443`** |  (CDN) 支持 Azure 内容分发网络所需的 MCR 存储。 |
| **`management.usgovcloudapi.net`**                      | **`HTTPS:443`** | 对于 Azure API Kubernetes 操作是必需的。 |
| **`login.microsoftonline.us`**                          | **`HTTPS:443`** | Azure Active Directory 身份验证所必需的。 |
| **`packages.microsoft.com`**                            | **`HTTPS:443`** | 此地址是用于缓存 apt-get 操作的 Microsoft 包存储库。  示例包包括 Moby、PowerShell 和 Azure CLI。 |
| **`acs-mirror.azureedge.net`**                          | **`HTTPS:443`** | 此地址用于安装所需二进制文件（如 kubenet 和 Azure CNI）所需的存储库。 |

## <a name="optional-recommended-fqdn--application-rules-for-aks-clusters"></a>适用于 AKS 群集的可选建议 FQDN/应用程序规则

以下 FQDN/应用程序规则是可选的，但建议用于 AKS 群集：

| 目标 FQDN                                                               | 端口          | 用途      |
|--------------------------------------------------------------------------------|---------------|----------|
| **`security.ubuntu.com`, `azure.archive.ubuntu.com`, `changelogs.ubuntu.com`** | **`HTTP:80`** | 此地址允许 Linux 群集节点下载所需的安全修补程序和更新。 |

如果选择阻止/不允许这些 Fqdn，则节点将仅在你进行[节点映像升级](node-image-upgrade.md)或[群集升级](upgrade-cluster.md)时接收 OS 更新。

## <a name="gpu-enabled-aks-clusters"></a>已启用 GPU AKS 群集

### <a name="required-fqdn--application-rules"></a>必需的 FQDN/应用程序规则

启用了 GPU 的 AKS 群集需要以下 FQDN/应用程序规则：

| 目标 FQDN                        | 端口      | 用途      |
|-----------------------------------------|-----------|----------|
| **`nvidia.github.io`**                  | **`HTTPS:443`** | 此地址用于在基于 GPU 的节点上正确安装和操作驱动程序。 |
| **`us.download.nvidia.com`**            | **`HTTPS:443`** | 此地址用于在基于 GPU 的节点上正确安装和操作驱动程序。 |
| **`apt.dockerproject.org`**             | **`HTTPS:443`** | 此地址用于在基于 GPU 的节点上正确安装和操作驱动程序。 |

## <a name="windows-server-based-node-pools"></a>基于 Windows Server 的节点池 

### <a name="required-fqdn--application-rules"></a>必需的 FQDN/应用程序规则

使用基于 Windows 服务器的节点池需要以下 FQDN/应用程序规则：

| 目标 FQDN                                                           | 端口      | 用途      |
|----------------------------------------------------------------------------|-----------|----------|
| **`onegetcdn.azureedge.net, go.microsoft.com`**                            | **`HTTPS:443`** | 安装与 windows 相关的二进制文件 |
| **`*.mp.microsoft.com, www.msftconnecttest.com, ctldl.windowsupdate.com`** | **`HTTP:80`**   | 安装与 windows 相关的二进制文件 |

## <a name="aks-addons-and-integrations"></a>AKS 加载项和集成

### <a name="azure-monitor-for-containers"></a>用于容器的 Azure Monitor

有两个选项可用于为容器 Azure Monitor 提供访问权限，你可以允许 Azure Monitor [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) **或**提供对所需的 FQDN/应用程序规则的访问权限。

#### <a name="required-network-rules"></a>所需网络规则

需要以下 FQDN/应用程序规则：

| 目标终结点                                                             | 协议 | 端口    | 用途  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureMonitor:443`**  | TCP           | 443      | 此终结点用于将指标数据和日志发送到 Azure Monitor 和 Log Analytics。 |

#### <a name="required-fqdn--application-rules"></a>必需的 FQDN/应用程序规则

启用适用于容器的 Azure Monitor 的 AKS 群集需要以下 FQDN/应用程序规则：

| FQDN                                    | 端口      | 用途      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | **`HTTPS:443`**    | 此终结点用于使用 Azure Monitor 的指标和监视遥测。 |
| *.ods.opinsights.azure.com    | **`HTTPS:443`**    | 此终结点由 Azure Monitor 用于引入 log analytics 数据。 |
| *.oms.opinsights.azure.com | **`HTTPS:443`** | 此终结点由 omsagent 使用，用于对 log analytics 服务进行身份验证。 |
| *.monitoring.azure.com | **`HTTPS:443`** | 此终结点用于将指标数据发送到 Azure Monitor。 |

### <a name="azure-dev-spaces"></a>Azure Dev Spaces

更新防火墙或安全配置，以允许与以下所有 Fqdn 和[Azure Dev Spaces 基础结构服务][dev-spaces-service-tags]的网络流量。

#### <a name="required-network-rules"></a>所需网络规则

| 目标终结点                                                             | 协议 | 端口    | 用途  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureDevSpaces`**  | TCP           | 443      | 此终结点用于将指标数据和日志发送到 Azure Monitor 和 Log Analytics。 |

#### <a name="required-fqdn--application-rules"></a>必需的 FQDN/应用程序规则 

启用 Azure Dev Spaces 的 AKS 群集需要以下 FQDN/应用程序规则：

| FQDN                                    | 端口      | 用途      |
|-----------------------------------------|-----------|----------|
| `cloudflare.docker.com` | **`HTTPS:443`** | 此地址用于拉取 linux alpine 和其他 Azure Dev Spaces 映像 |
| `gcr.io` | **`HTTPS:443`** | 此地址用于拉取 helm/tiller 映像 |
| `storage.googleapis.com` | **`HTTPS:443`** | 此地址用于拉取 helm/tiller 映像 |


### <a name="azure-policy-preview"></a>Azure 策略 (预览) 

> [!CAUTION]
> 下面的某些功能处于预览阶段。  随着该功能进入公开预览和未来发布阶段，本文中的建议可能会发生变化。

#### <a name="required-fqdn--application-rules"></a>必需的 FQDN/应用程序规则 

启用 Azure Policy 的 AKS 群集需要以下 FQDN/应用程序规则。

| FQDN                                          | 端口      | 用途      |
|-----------------------------------------------|-----------|----------|
| **`gov-prod-policy-data.trafficmanager.net`** | **`HTTPS:443`** | 此地址用于 Azure Policy 的正确操作。 （目前在 AKS 中以预览版提供。） |
| **`raw.githubusercontent.com`**               | **`HTTPS:443`** | 此地址用于从 GitHub 中提取内置策略，以确保 Azure Policy 的正确操作。 （目前在 AKS 中以预览版提供。） |
| **`dc.services.visualstudio.com`**            | **`HTTPS:443`** | 将遥测数据发送到 Applications Insights 终结点的 Azure Policy 加载项。 |


## <a name="restrict-egress-traffic-using-azure-firewall"></a>使用 Azure 防火墙限制出站流量

Azure 防火墙提供 Azure Kubernetes 服务 (`AzureKubernetesService`) FQDN 标记以简化此配置。 

> [!NOTE]
> FQDN 标记包含上面列出的所有 Fqdn，并自动保持最新。

下面是部署的示例体系结构：

![锁定拓扑](media/limit-egress-traffic/aks-azure-firewall-egress.png)

* 公共入口强制流过防火墙筛选器
  * AKS 代理节点在专用子网中隔离。
  * [Azure 防火墙](../firewall/overview.md)部署在其自己的子网中。
  * DNAT 规则将 FW 公共 IP 转换为 LB 前端 IP。
* 出站请求使用[用户定义的路由](egress-outboundtype.md)从代理节点开始到 Azure 防火墙内部 IP
  * 来自 AKS 代理节点的请求遵循 UDR，该 UDR 位于 AKS 群集部署到的子网中。
  * Azure 防火墙从公共 IP 前端中的虚拟网络出口流量
  * 对公共 Internet 或其他 Azure 服务的访问流入和流出防火墙前端 IP 地址
  * （可选）对 AKS 控制平面的访问受[API 服务器授权的 ip 范围](./api-server-authorized-ip-ranges.md)（包括防火墙公共前端 ip 地址）的保护。
* 内部流量
  * 或者，除了[公共负载均衡器](load-balancer-standard.md)外，还可以使用[内部负载均衡器](internal-lb.md)来实现内部流量，也可以在其自身的子网中隔离。


以下步骤使用 Azure 防火墙的 `AzureKubernetesService` FQDN 标记来限制来自 AKS 群集的出站流量，并提供一个示例，说明如何通过防火墙配置公用入站流量。


### <a name="set-configuration-via-environment-variables"></a>通过环境变量设置配置

定义要在资源创建中使用的一组环境变量。

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

预配一个虚拟网络，其中包含两个单独的子网，一个用于群集，一个用于防火墙。 还可以选择为内部服务入口创建一个。

![空网络拓扑](media/limit-egress-traffic/empty-network.png)

创建资源组以保存所有资源。

```azure-cli
# Create Resource Group

az group create --name $RG --location $LOC
```

创建包含两个子网的虚拟网络，以便托管 AKS 群集和 Azure 防火墙。 各虚拟网络都有自己的子网。 让我们从 AKS 网络开始。

```
# Dedicated virtual network with AKS subnet

az network vnet create \
    --resource-group $RG \
    --name $VNET_NAME \
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

### <a name="create-and-set-up-an-azure-firewall-with-a-udr"></a>使用 UDR 创建和设置 Azure 防火墙

必须配置 Azure 防火墙入站和出站规则。 防火墙的主要用途是使组织能够在 AKS 群集中配置具体的入口和出口流量规则。

![防火墙和 UDR](media/limit-egress-traffic/firewall-udr.png)


> [!IMPORTANT]
> 如果群集或应用程序创建多个定向到相同或小型目标的出站连接，则可能需要更多的防火墙前端 Ip，以避免支持每个前端 IP 的端口。
> 有关如何创建具有多个 Ip 的 Azure 防火墙的详细信息，请参阅[**此处**](../firewall/quick-create-multiple-ip-template.md)

创建将用作 Azure 防火墙前端地址的标准 SKU 公共 IP 资源。

```azure-cli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

注册预览版 CLI 扩展以创建 Azure 防火墙。
```azure-cli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC --enable-dns-proxy true
```
现可将之前创建的 IP 地址分配到防火墙前端。

> [!NOTE]
> 设置 Azure 防火墙的公共 IP 地址可能需要几分钟的时间。
> 若要对网络规则使用 FQDN，需要启用 DNS 代理，当启用时，防火墙会在端口53上侦听，并将 DNS 请求转发到上面指定的 DNS 服务器。 这将允许防火墙自动转换该 FQDN。

```azure-cli
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
> 如果对具有[授权 ip 地址范围](./api-server-authorized-ip-ranges.md)的 AKS API 服务器使用安全访问，则需要将防火墙公共 ip 添加到授权 ip 范围。

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>使用到 Azure 防火墙的跃点创建 UDR

Azure 自动在 Azure 子网、虚拟网络与本地网络之间路由流量。 若要更改 Azure 的任何默认路由，可以创建一个路由表。

创建一个要与给定子网关联的空路由表。 该路由表将把下一个跃点定义为前面创建的 Azure 防火墙。 每个子网可以有一个与之关联的路由表，也可以没有。

```azure-cli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

请参阅[虚拟网络路由表文档](../virtual-network/virtual-networks-udr-overview.md#user-defined)，了解如何替代 Azure 的默认系统路由或向子网的路由表添加其他路由。

### <a name="adding-firewall-rules"></a>添加防火墙规则

下面是可用于在防火墙上配置的三个网络规则，你可能需要根据你的部署来修改这些规则。 第一个规则允许通过 TCP 访问端口9000。 第二个规则允许通过 UDP (访问端口1194和123。如果要部署到 Azure 中国世纪互联，可能需要[更多](#azure-china-21vianet-required-network-rules)) 。 这两个规则将仅允许目标为当前使用的 Azure 区域 CIDR 的流量，在本例中为 "美国东部"。 最后，我们将通过 UDP 添加第三个网络规则，以打开端口123到 `ntp.ubuntu.com` FQDN (添加 FQDN 作为网络规则是 Azure 防火墙的一项特定功能，并且在使用自己的选项) 时，需要对其进行调整。

设置网络规则后，还将使用来添加应用程序规则，该规则 `AzureKubernetesService` 涵盖通过 TCP 端口443和端口80可访问的所有必需 fqdn。

```
# Add FW Network Rules

az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apiudp' --protocols 'UDP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 1194 --action allow --priority 100
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apitcp' --protocols 'TCP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 9000
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'time' --protocols 'UDP' --source-addresses '*' --destination-fqdns 'ntp.ubuntu.com' --destination-ports 123

# Add FW Application Rules

az network firewall application-rule create -g $RG -f $FWNAME --collection-name 'aksfwar' -n 'fqdn' --source-addresses '*' --protocols 'http=80' 'https=443' --fqdn-tags "AzureKubernetesService" --action allow --priority 100
```

请参阅 [Azure 防火墙文档](../firewall/overview.md)，了解有关 Azure 防火墙服务的详细信息。

### <a name="associate-the-route-table-to-aks"></a>将路由表关联到 AKS

要将群集与防火墙关联，群集子网的专用子网必须引用前面创建的路由表。 可以向群集和防火墙所在的虚拟网络发出命令来更新群集子网的路由表，以此实现关联。

```azure-cli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

### <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>将 UDR 出站类型的 AKS 部署到现有网络

现在，可以将 AKS 群集部署到现有的虚拟网络中。 我们还将使用[出站 `userDefinedRouting` 类型](egress-outboundtype.md)，此功能可确保任何出站流量都将通过防火墙强制进行，并且默认情况下不存在其他传出路径 (可以) 使用负载均衡器出站类型。

![aks-deploy](media/limit-egress-traffic/aks-udr-fw.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>创建一个服务主体，使其有权访问现有虚拟网络中的预配资源

AKS 使用服务主体来创建群集资源。 在创建时传递的服务主体用于创建 AKS 所使用的基本 AKS 资源（例如存储资源、Ip 和负载均衡器） (你也可以改为使用[托管标识](use-managed-identity.md)) 。 如果未在下面授予适当的权限，则无法预配 AKS 群集。

```azure-cli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

现在，将下面的 `APPID` 和 `PASSWORD` 替换为由前面的命令输出自动生成的服务主体 appid 和服务主体密码。 我们将引用 VNET 资源 ID 来向服务主体授予权限，以便 AKS 可以将资源部署到其中。

```azure-cli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role "Network Contributor"
```

你可以在[此处](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)查看所需的详细权限。

> [!NOTE]
> 如果你使用的是 kubenet 网络插件，则需要为预先创建的路由表指定 AKS 服务主体或托管标识权限，因为 kubenet 需要一个路由表来添加必要路由规则。 
> ```azurecli-interactive
> RTID=$(az network route-table show -g $RG -n $FWROUTE_TABLE_NAME --query id -o tsv)
> az role assignment create --assignee $APPID --scope $RTID --role "Network Contributor"
> ```

### <a name="deploy-aks"></a>部署 AKS

最后，可以将 AKS 群集部署到专用于群集的现有子网中。 要在其中部署的目标子网是使用环境变量 `$SUBNETID` 定义的。 在前面的步骤中，我们未定义 `$SUBNETID` 变量。 要设置子网 ID 的值，可以使用以下命令：

```azurecli
SUBNETID=$(az network vnet subnet show -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --query id -o tsv)
```

将使用子网中已存在的 UDR 定义出站类型。 此配置将使 AKS 跳过负载均衡器的设置和 IP 设置。

> [!IMPORTANT]
> 有关出站类型 UDR （包括限制）的详细信息，请参阅[**出局出站类型 UDR**](egress-outboundtype.md#limitations)。


> [!TIP]
> 可以向群集部署（如[**专用群集**](private-clusters.md)）添加其他功能。 
>
> 可添加[**api 服务器授权 IP 范围**](api-server-authorized-ip-ranges.md)的 AKS 功能，以限制仅限访问防火墙公共终结点的 api 服务器。 授权 IP 范围功能在关系图中表示为可选。 启用授权 IP 范围功能以限制 API 服务器访问权限时，开发者工具必须使用防火墙的虚拟网络中的 jumpbox，或者必须将所有开发者终结点添加到授权 IP 范围。

```azure-cli
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

### <a name="enable-developer-access-to-the-api-server"></a>启用对 API 服务器的开发者访问权限

如果在上一步中使用了群集的授权 IP 范围，则必须将开发人员工具的 IP 地址添加到已批准 IP 范围的 AKS 群集列表中，以便从该列表访问 API 服务器。 另一种方法是使用位于防火墙的虚拟网络中的单独子网内的所需工具配置 jumpbox。

用以下命令将其他 IP 地址添加到已批准的范围

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKS_NAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 使用 [az aks get-凭据] [az-aks] 命令将配置 `kubectl` 为连接到新创建的 Kubernetes 群集。 

 ```azure-cli
 az aks get-credentials -g $RG -n $AKS_NAME
 ```

### <a name="deploy-a-public-service"></a>部署公共服务
你现在可以开始公开服务并将应用程序部署到此群集。 在此示例中，我们将公开一个公共服务，但你也可以选择通过[内部负载均衡器](internal-lb.md)公开内部服务。

![公共服务 DNAT](media/limit-egress-traffic/aks-create-svc.png)

将以下 yaml 复制到名为 `example.yaml` 的文件，部署 Azure 投票应用。

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

通过运行以下代码部署服务：

```bash
kubectl apply -f example.yaml
```

### <a name="add-a-dnat-rule-to-azure-firewall"></a>将 DNAT 规则添加到 Azure 防火墙

> [!IMPORTANT]
> 当使用 Azure 防火墙来限制出口流量并创建一个用户定义的路由 (UDR) 来强制应用于所有出口流量时，请确保在防火墙中创建了适当的 DNAT 规则来正确地允许入口流量。 使用带有 UDR 的 Azure 防火墙会由于非对称路由而中断入口设置。 （如果 AKS 子网的默认路由指向防火墙的专用 IP 地址，但使用的是公共负载均衡器 - 传入或 Kubernetes 服务类型：LoadBalancer）。 在这种情况下，将通过负载均衡器的公共 IP 地址接收传入的负载均衡器流量，但返回路径将通过防火墙的专用 IP 地址。 由于防火墙是有状态的，并且无法识别已建立的会话，因此会丢弃返回的数据包。 若要了解如何将 Azure 防火墙与入口或服务负载均衡器集成，请参阅[将 Azure 防火墙与 Azure 标准负载均衡器集成](../firewall/integrate-lb.md)。


要配置入站连接，必须将 DNAT 规则写入 Azure 防火墙。 若要测试与群集的连接，请为防火墙前端公共 IP 地址定义一个规则，以便路由到由内部服务公开的内部 IP。

可以自定义目标地址，因为它是防火墙上要访问的端口。 转换后的地址必须是内部负载均衡器的 IP 地址。 转换后的端口必须是 Kubernetes 服务的已公开的端口。

你需要指定分配给 Kubernetes 服务创建的负载均衡器的内部 IP 地址。 通过运行以下代码检索地址：

```bash
kubectl get services
```

所需的 IP 地址将列在“EXTERNAL-IP”列中，如下所示。

```bash
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes         ClusterIP      10.41.0.1       <none>        443/TCP        10h
voting-analytics   ClusterIP      10.41.88.129    <none>        8080/TCP       9m
voting-app         LoadBalancer   10.41.185.82    20.39.18.6    80:32718/TCP   9m
voting-storage     ClusterIP      10.41.221.201   <none>        3306/TCP       9m
```

通过运行以下内容获取服务 IP：
```bash
SERVICE_IP=$(k get svc voting-app -o jsonpath='{.status.loadBalancer.ingress[*].ip}')
```

通过运行以下内容添加 NAT 规则：
```azure-cli
az network firewall nat-rule create --collection-name exampleset --destination-addresses $FWPUBLIC_IP --destination-ports 80 --firewall-name $FWNAME --name inboundrule --protocols Any --resource-group $RG --source-addresses '*' --translated-port 80 --action Dnat --priority 100 --translated-address $SERVICE_IP
```

### <a name="validate-connectivity"></a>验证连接

在浏览器中导航到 Azure 防火墙前端 IP 地址，以验证连接。

应会看到 AKS 投票应用程序。 在此示例中，防火墙公共 IP 为 `52.253.228.132` 。


![aks-投票](media/limit-egress-traffic/aks-vote.png)


### <a name="clean-up-resources"></a>清理资源

要清理 Azure 资源，请删除 AKS 资源组。

```azure-cli
az group delete -g $RG
```

## <a name="next-steps"></a>后续步骤

本文介绍了在要限制群集的出口流量时允许哪些端口和地址。 还了解了如何使用 Azure 防火墙保护出站流量。 

如果需要，可以在[出站类型 `userDefinedRoute` 文档](egress-outboundtype.md)后通用化以上步骤，将流量转发到首选出口解决方案。

如果你想要限制 pod 在其自身与每个西部流量限制之间通信的方式，请参阅[在 AKS 中使用网络策略的 pod 之间的安全流量][network-policy]。

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
