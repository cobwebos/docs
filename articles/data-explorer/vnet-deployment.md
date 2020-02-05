---
title: 将 Azure 数据资源管理器部署到虚拟网络（预览版）
description: 了解如何在虚拟网络中部署 Azure 数据资源管理器
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 28b9c55df8cd7883e05e964b8b67e08c7a3eb8c1
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74812725"
---
# <a name="deploy-azure-data-explorer-into-your-virtual-network-preview"></a>将 Azure 数据资源管理器部署到虚拟网络（预览版）

本文介绍将 Azure 数据资源管理器群集部署到自定义 Azure 虚拟网络时存在的资源。 此信息将帮助你将群集部署到虚拟网络（VNet）中的子网。 有关 Azure 虚拟网络的详细信息，请参阅[什么是 Azure 虚拟网络？](/azure/virtual-network/virtual-networks-overview)

   ![vnet 关系图](media/vnet-deployment/vnet-diagram.png)

Azure 数据资源管理器支持将群集部署到虚拟网络（VNet）中的子网。 此功能使你能够：

* 在 Azure 数据资源管理器群集流量上强制实施[网络安全组](/azure/virtual-network/security-overview)（NSG）规则。
* 将本地网络连接到 Azure 数据资源管理器群集的子网。
* 通过[服务终结点](/azure/virtual-network/virtual-network-service-endpoints-overview)保护数据连接源（[事件中心](/azure/event-hubs/event-hubs-about)和[事件网格](/azure/event-grid/overview)）。

> [!NOTE]
> 虚拟网络集成和部署处于预览模式。 若要启用此功能，请打开[支持票证](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。

## <a name="access-your-azure-data-explorer-cluster-in-your-vnet"></a>在 VNet 中访问 Azure 数据资源管理器群集

你可以使用以下每个服务的 IP 地址（引擎和数据管理服务）访问 Azure 数据资源管理器群集：

* **专用 IP**：用于访问 VNet 中的群集。
* **公共 IP**：用于从 VNet 外部访问群集以进行管理和监视，并用作从群集启动的出站连接的源地址。

创建以下 DNS 记录以访问该服务： 

* `[clustername].[geo-region].kusto.windows.net` （引擎） `ingest-[clustername].[geo-region].kusto.windows.net` （数据管理）映射到每个服务的公共 IP。 

* `private-[clustername].[geo-region].kusto.windows.net` （引擎） `private-ingest-[clustername].[geo-region].kusto.windows.net` （数据管理）映射到每个服务的专用 IP。

## <a name="plan-subnet-size-in-your-vnet"></a>规划 VNet 中的子网大小

部署子网后，无法更改用于承载 Azure 数据资源管理器群集的子网的大小。 在 VNet 中，Azure 数据资源管理器为每个 VM 使用一个专用 IP 地址，并为内部负载均衡器（引擎和数据管理）使用两个专用 IP 地址。 Azure 网络还为每个子网使用5个 IP 地址。 Azure 数据资源管理器为数据管理服务预配两个 Vm。 引擎服务 Vm 按用户配置规模容量进行设置。

IP 地址的总数：

| 用途 | 地址数 |
| --- | --- |
| 引擎服务 | 每个实例1个 |
| 数据管理服务 | 2 |
| 内部负载均衡器 | 2 |
| Azure 保留地址 | 5 |
| **总计** | **#engine_instances + 9** |

> [!IMPORTANT]
> 必须提前计划子网大小，因为在部署 Azure 数据资源管理器后无法更改它。 因此，请相应地保留所需的子网大小。

## <a name="service-endpoints-for-connecting-to-azure-data-explorer"></a>用于连接到 Azure 数据资源管理器的服务终结点

[Azure 服务终结点](/azure/virtual-network/virtual-network-service-endpoints-overview)可让你将 azure 多租户资源保护到虚拟网络。
若要在子网中部署 Azure 数据资源管理器群集，可以在限制 Azure 数据资源管理器子网的基础资源的情况下，设置[事件中心](/azure/event-hubs/event-hubs-about)或[事件网格](/azure/event-grid/overview)的数据连接。

> [!NOTE]
> 将 EventGrid 安装程序与[存储](/azure/storage/common/storage-introduction)和 [事件中心] 结合使用时，可以使用服务终结点将订阅中使用的存储帐户锁定到 Azure 数据资源管理器的子网，同时允许在[防火墙配置](/azure/storage/common/storage-network-security)中使用受信任的 azure 平台服务，但是事件中心无法启用服务终结点，因为它不支持受信任的[azure 平台服务](/azure/event-hubs/event-hubs-service-endpoints)。

## <a name="dependencies-for-vnet-deployment"></a>VNet 部署的依赖项

### <a name="network-security-groups-configuration"></a>网络安全组配置

[网络安全组（NSG）](/azure/virtual-network/security-overview)提供了控制 VNet 中的网络访问的功能。 可以使用两个终结点访问 Azure 数据资源管理器： HTTPs （443）和 TDS （1433）。 必须将以下 NSG 规则配置为允许访问这些终结点，以便对群集进行管理、监视和正确操作。

#### <a name="inbound-nsg-configuration"></a>入站 NSG 配置

| **使用**   | **From**   | **收件人**   | 协议   |
| --- | --- | --- | --- |
| 管理  |[ADX 管理地址](#azure-data-explorer-management-ip-addresses)/AzureDataExplorerManagement （ServiceTag） | ADX 子网：443  | TCP  |
| 运行状况监视  | [ADX 运行状况监视地址](#health-monitoring-addresses)  | ADX 子网：443  | TCP  |
| ADX 内部通信  | ADX 子网：所有端口  | ADX 子网：所有端口  | 所有  |
| 允许 Azure 负载均衡器入站（运行状况探测）  | AzureLoadBalancer  | ADX 子网：80443  | TCP  |

#### <a name="outbound-nsg-configuration"></a>出站 NSG 配置

| **使用**   | **From**   | **收件人**   | 协议   |
| --- | --- | --- | --- |
| 依赖于 Azure 存储  | ADX 子网  | 存储：443  | TCP  |
| 依赖关系 Azure Data Lake  | ADX 子网  | AzureDataLake：443  | TCP  |
| EventHub 引入和服务监视  | ADX 子网  | EventHub：443，5671  | TCP  |
| 发布指标  | ADX 子网  | AzureMonitor：443 | TCP  |
| Azure Monitor 配置下载  | ADX 子网  | [Azure Monitor 配置终结点地址](#azure-monitor-configuration-endpoint-addresses)：443 | TCP  |
| Active Directory （如果适用） | ADX 子网 | AzureActiveDirectory：443 | TCP |
| 证书颁发机构 | ADX 子网 | Internet：80 | TCP |
| 内部通信  | ADX 子网  | ADX 子网：所有端口  | 所有  |
| 用于 `sql\_request` 和 `http\_request` 插件的端口  | ADX 子网  | Internet：自定义  | TCP  |

### <a name="relevant-ip-addresses"></a>相关 IP 地址

#### <a name="azure-data-explorer-management-ip-addresses"></a>Azure 数据资源管理器管理 IP 地址

| 地区 | 地址 |
| --- | --- |
| 澳大利亚中部 | 20.37.26.134 |
| 澳大利亚 Central2 | 20.39.99.177 |
| 澳大利亚东部 | 40.82.217.84 |
| 澳大利亚东南部 | 20.40.161.39 |
| BrazilSouth | 191.233.25.183 |
| 加拿大中部 | 40.82.188.208 |
| 加拿大东部 | 40.80.255.12 |
| 印度中部 | 40.81.249.251 |
| 美国中部 | 40.67.188.68 |
| 美国中部 EUAP | 40.89.56.69 |
| 东亚 | 20.189.74.103 |
| 美国东部 | 52.224.146.56 |
| 美国东部 2 | 52.232.230.201 |
| 东 2 EUAP | 52.253.226.110 |
| 法国中部 | 40.66.57.91 |
| 法国南部 | 40.82.236.24 |
| 日本东部 | 20.43.89.90 |
| 日本西部 | 40.81.184.86 |
| 韩国中部 | 40.82.156.149 |
| 韩国南部 | 40.80.234.9 |
| 美国中北部 | 40.81.45.254 |
| 北欧 | 52.142.91.221 |
| 南非北部 | 102.133.129.138 |
| 南非西部 | 102.133.0.97 |
| 美国中南部 | 20.45.3.60 |
| 东南亚 | 40.119.203.252 |
| 印度南部 | 40.81.72.110 |
| 英国南部 | 40.81.154.254 |
| 英国西部 | 40.81.122.39 |
| 美国中西部 | 52.159.55.120 |
| 西欧 | 51.145.176.215 |
| 印度西部 | 40.81.88.112 |
| 美国西部 | 13.64.38.225 |
| 美国西部 2 | 40.90.219.23 |

#### <a name="health-monitoring-addresses"></a>运行状况监视地址

| 地区 | 地址 |
| --- | --- |
| 澳大利亚中部 | 191.239.64.128 |
| 澳大利亚中部 2 | 191.239.64.128 |
| 澳大利亚东部 | 191.239.64.128 |
| 澳大利亚东南部 | 191.239.160.47 |
| 巴西南部 | 23.98.145.105 |
| 加拿大中部 | 168.61.212.201 |
| 加拿大东部 | 168.61.212.201 |
| 印度中部 | 23.99.5.162 |
| 美国中部 | 168.61.212.201 |
| 美国中部 EUAP | 168.61.212.201 |
| 东亚 | 168.63.212.33 |
| 美国东部 | 137.116.81.189 |
| 美国东部 2 | 137.116.81.189 |
| 美国东部 2 EUAP | 137.116.81.189 |
| 法国中部 | 23.97.212.5 |
| 法国南部 | 23.97.212.5 |
| 日本东部 | 138.91.19.129 |
| 日本西部 | 138.91.19.129 |
| 韩国中部 | 138.91.19.129 |
| 韩国南部 | 138.91.19.129 |
| 美国中北部 | 23.96.212.108 |
| 北欧 | 191.235.212.69 
| 南非北部 | 104.211.224.189 |
| 南非西部 | 104.211.224.189 |
| 美国中南部 | 23.98.145.105 |
| 印度南部 | 23.99.5.162 |
| 东南亚 | 168.63.173.234 |
| 英国南部 | 23.97.212.5 |
| 英国西部 | 23.97.212.5 |
| 美国中西部 | 168.61.212.201 |
| 西欧 | 23.97.212.5 |
| 印度西部 | 23.99.5.162 |
| 美国西部 | 23.99.5.162 |
| 美国西部 2 | 23.99.5.162 | 

#### <a name="azure-monitor-configuration-endpoint-addresses"></a>Azure Monitor 配置终结点地址

| 地区 | 地址 |
| --- | --- |
| 澳大利亚中部 | 52.148.86.165 |
| 澳大利亚中部2 | 52.148.86.165 |
| 澳大利亚东部 | 52.148.86.165 |
| 澳大利亚东南部 | 52.148.86.165 |
| 巴西南部 | 13.68.89.19 |
| 加拿大中部 | 13.90.43.231 |
| 加拿大东部 | 13.90.43.231 |
| 印度中部 | 13.71.25.187 |
| 美国中部 | 52.173.95.68 |
| 美国中部 EUAP | 13.90.43.231 |
| 东亚 | 13.75.117.221 |
| 美国东部 | 13.90.43.231 |
| 美国东部2 | 13.68.89.19 | 
| 美国东部 2 EUAP | 13.68.89.19 |
| 法国中部 | 52.174.4.112 |
| 法国南部 | 52.174.4.112 |
| 日本东部 | 13.75.117.221 |
| 日本西部 | 13.75.117.221 |
| 韩国中部 | 13.75.117.221 |
| 韩国南部 | 13.75.117.221 |
| 美国中北部 | 52.162.240.236 |
| 北欧 | 52.169.237.246 |
| 南非北部 | 13.71.25.187 |
| 南非西部 | 13.71.25.187 |
| 美国中南部 | 13.84.173.99 |
| 印度南部 | 13.71.25.187 |
| 东南亚 | 52.148.86.165 |
| 英国南部 | 52.174.4.112 |
| 英国西部 | 52.169.237.246 |
| 美国中西部 | 52.161.31.69 |
| 西欧 | 52.174.4.112 |
| 印度西部 | 13.71.25.187 |
| 美国西部 | 40.78.70.148 |
| 美国西部2 | 52.151.20.103 |

## <a name="expressroute-setup"></a>ExpressRoute 设置

使用 ExpressRoute 将本地网络连接到 Azure 虚拟网络。 常见的设置是通过边界网关协议（BGP）会话播发默认路由（0.0.0.0/0）。 这会强制将来自虚拟网络的流量转发到客户的本地网络，该网络可能会丢弃流量，导致出站流中断。 若要解决此默认值，可配置[用户定义的路由（UDR）](/azure/virtual-network/virtual-networks-udr-overview#user-defined) （0.0.0.0/0），并将下一个跃点设置为*Internet*。 由于 UDR 优先于 BGP，因此流量将发送到 Internet。

## <a name="securing-outbound-traffic-with-firewall"></a>用防火墙保护出站流量

如果要使用[Azure 防火墙](/azure/firewall/overview)或任何虚拟设备来保护出站流量以限制域名，则必须在防火墙中允许下列完全限定的域名（FQDN）。

```
prod.warmpath.msftcloudes.com:443
production.diagnostics.monitoring.core.windows.net:443
graph.windows.net:443
*.update.microsoft.com:443
shavamanifestcdnprod1.azureedge.net:443
login.live.com:443
wdcp.microsoft.com:443
login.microsoftonline.com:443
azureprofilerfrontdoor.cloudapp.net:443
*.core.windows.net:443
*.servicebus.windows.net:443
shoebox2.metrics.nsatc.net:443
prod-dsts.dsts.core.windows.net:443
ocsp.msocsp.com:80
*.windowsupdate.com:80
ocsp.digicert.com:80
go.microsoft.com:80
dmd.metaservices.microsoft.com:80
www.msftconnecttest.com:80
crl.microsoft.com:80
www.microsoft.com:80
adl.windows.com:80
crl3.digicert.com:80
```

还需要在子网中定义[路由表](/azure/virtual-network/virtual-networks-udr-overview)，其中包含[管理地址](#azure-data-explorer-management-ip-addresses)和[运行状况监视地址](#health-monitoring-addresses)和下一跃点*Internet* ，以防止非对称路由问题。

例如，对于**美国西部**区域，必须定义以下 udr：

| 名称 | 地址前缀 | 下一个跃点 |
| --- | --- | --- |
| ADX_Management | 13.64.38.225/32 | Internet |
| ADX_Monitoring | 23.99.5.162/32 | Internet |

## <a name="deploy-azure-data-explorer-cluster-into-your-vnet-using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板将 Azure 数据资源管理器群集部署到 VNet

若要将 Azure 数据资源管理器群集部署到虚拟网络，请使用将[azure 数据资源管理器群集部署到 VNet](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) Azure 资源管理器模板。

此模板创建群集、虚拟网络、子网、网络安全组和公共 IP 地址。
