---
title: "使用 Site Recovery 进行 VMware 到 Azure 的复制时的网络规划 | Microsoft Docs"
description: "本文介绍在使用 Azure Site Recovery 复制 Azure VM 时所要进行的网络规划"
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/01/2017
ms.author: sujayt
ms.openlocfilehash: 7b37e853f6b97ba313111f9201f877846a28fae9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="step-3-plan-networking-for-azure-vm-replication"></a>步骤 3：为 Azure VM 复制规划网络

确认[部署先决条件](azure-to-azure-walkthrough-prerequisites.md)后，请阅读本文了解在使用 Azure Site Recovery 服务将 Azure 虚拟机 (VM) 从一个 Azure 区域复制和恢复到另一个 Azure 区域时的网络注意事项。 

- 完成本文后，应该会清楚地知道如何为想要复制的 Azure VM 设置出站访问，以及如何从本地站点连接到这些 VM。
- 请将任何评论发布到本文底部，或者在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中提问。

>[!NOTE]
> 使用 Site Recovery 复制 Azure VM 的功能目前处于预览状态。



## <a name="network-overview"></a>网络概述

通常，Azure VM 位于 Azure 虚拟网络/子网中，本地站点与 Azure 之间使用 Azure ExpressRoute 或 VPN 建立了连接。

网络通常使用防火墙和/或网络安全组 (NSG) 进行保护。 防火墙可以使用基于 URL 的 IP 列表来控制网络连接。 NSG 使用基于 IP 范围的规则。 


要使 Site Recovery 按预期工作，需在想要复制的 VM 中对出站网络连接进行一些更改。 Site Recovery 不支持使用身份验证代理来控制网络连接。 如果使用身份验证代理，则无法启用复制。 


下图描绘了 Azure VM 上运行的应用程序的典型环境。

![customer-environment](./media/azure-to-azure-walkthrough-network/source-environment.png)

**Azure VM 环境**

你还可能使用 Azure ExpressRoute 或 VPN 连接建立了从本地站点到 Azure 的连接。 

![customer-environment](./media/azure-to-azure-walkthrough-network/source-environment-expressroute.png)

**本地到 Azure 的连接**



## <a name="outbound-connectivity-for-urls"></a>URL 的出站连接

如果使用基于 URL 的防火墙代理来控制出站连接，请确保允许 Site Recovery 使用这些 URL

**URL** | **详细信息**  
--- | ---
*.blob.core.windows.net | 允许将数据从 VM 写入源区域中的缓存存储帐户。
login.microsoftonline.com | 向 Site Recovery 服务 URL 提供授权和身份验证。
*.hypervrecoverymanager.windowsazure.com | 允许从 VM 与 Site Recovery 服务通信。
*.servicebus.windows.net | 必需，以便从 VM 写入 Site Recovery 监视和诊断数据。

## <a name="outbound-connectivity-for-ip-address-ranges"></a>IP 地址范围的出站连接

- 可以通过下载并运行[此脚本](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702)在 NSG 上自动创建所需的规则。
- 在生产 NSG 中创建所需的 NSG 规则之前，建议先在测试 NSG 中创建这些规则，并确保没有任何问题。
- 若要创建所需的 NSG 规则数，请确保订阅已列入允许列表。 联系支持人员，提高订阅中的 NSG 规则限制。
如果使用任何基于 IP 的防火墙代理或 NSG 规则控制出站连接，则需要将以下 IP 范围列入白名单，具体取决于虚拟机的源位置和目标位置：

    - 对应于源位置的所有 IP 地址范围。 下载[范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)。必须列入允许列表，才能从 VM 将数据写入到缓存存储帐户。
    - 对应于 Office 365 [身份验证和标识 IP V4 终结点](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)的所有 IP 范围。 如果向 Office 365 IP 范围添加了新的 IP，则需要创建新的 NSG 规则。
-     Site Recovery 服务终结点 IP 地址（[以 XML 文件形式提供](https://aka.ms/site-recovery-public-ips)），具体取决于目标位置： 

   **目标位置** | **Site Recovery 服务 IP** |  **Site Recovery 监视 IP**
   --- | --- | ---
   东亚 | 52.175.17.132</br>40.83.121.61 | 13.94.47.61
   东南亚 | 52.187.58.193</br>52.187.169.104 | 13.76.179.223
   印度中部 | 52.172.187.37</br>52.172.157.193 | 104.211.98.185
   印度南部 | 52.172.46.220</br>52.172.13.124 | 104.211.224.190
   美国中北部 | 23.96.195.247</br>23.96.217.22 | 168.62.249.226
   欧洲北部 | 40.69.212.238</br>13.74.36.46 | 52.169.18.8
   欧洲西部 | 52.166.13.64</br>52.166.6.245 | 40.68.93.145
   美国东部 | 13.82.88.226</br>40.71.38.173 | 104.45.147.24
   美国西部 | 40.83.179.48</br>13.91.45.163 | 104.40.26.199
   美国中南部 | 13.84.148.14</br>13.84.172.239 | 104.210.146.250
   美国中部 | 40.69.144.231</br>40.69.167.116 | 52.165.34.144
   美国东部 2 | 52.184.158.163</br>52.225.216.31 | 40.79.44.59
   日本东部 | 52.185.150.140</br>13.78.87.185 | 138.91.1.105
   日本西部 | 52.175.146.69</br>52.175.145.200 | 138.91.17.38
   巴西南部 | 191.234.185.172</br>104.41.62.15 | 23.97.97.36
   澳大利亚东部 | 104.210.113.114</br>40.126.226.199 | 191.239.64.144
   澳大利亚东南部 | 13.70.159.158</br>13.73.114.68 | 191.239.160.45
   加拿大中部 | 52.228.36.192</br>52.228.39.52 | 40.85.226.62
   加拿大东部 | 52.229.125.98</br>52.229.126.170 | 40.86.225.142
   美国中西部 | 52.161.20.168</br>13.78.230.131 | 13.78.149.209
   美国西部 2 | 52.183.45.166</br>52.175.207.234 | 13.66.228.204
   英国西部 | 51.141.3.203</br>51.140.226.176 | 51.141.14.113
   英国南部 | 51.140.43.158</br>51.140.29.146 | 51.140.189.52

## <a name="example-nsg-configuration"></a>NSG 配置示例

本部分介绍如何配置 NSG 规则，以便可以对 VM 使用复制。 如果使用 NSG 规则控制出站连接，请对所有必需的 IP 范围使用“允许 HTTPS 出站”规则。

在此示例中，VM 源位置为“美国东部”。 复制目标位置为“美国中部”。


### <a name="example"></a>示例

#### <a name="east-us"></a>美国东部

1. 创建对应于[美国东部 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)的规则。 必须执行此操作，才能从 VM 将数据写入到缓存存储帐户。
2. 为对应于 Office 365 [身份验证和标识 IP V4 终结点](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)的所有 IP 范围创建规则。
3. 创建对应于目标位置的规则：

   **位置** | **Site Recovery 服务 IP** |  **Site Recovery 监视 IP**
    --- | --- | ---
   美国中部 | 40.69.144.231</br>40.69.167.116 | 52.165.34.144

#### <a name="central-us"></a>美国中部

必须创建这些规则，才能在故障转移后启用从目标区域到源区域的复制。

1. 创建对应于[美国中部 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)的规则。
2. 为对应于 Office 365 [身份验证和标识 IP V4 终结点](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)的所有 IP 范围创建规则。
3. 创建对应于源位置的规则：

   **位置** | **Site Recovery 服务 IP** |  **Site Recovery 监视 IP**
    --- | --- | ---
   美国东部 | 13.82.88.226</br>40.71.38.173 | 104.45.147.24


## <a name="existing-on-premises-connection"></a>现有的本地连接

如果在本地站点与 Azure 中的源位置之间建立了 ExpressRoute 或 VPN 连接，请遵循以下准则：

**配置** | **详细信息**
--- | ---
**强制隧道** | 通常，默认路由 (0.0.0.0/0) 会强制出站 Internet 流量流经本地位置。 但我们不建议采用这种方式。 复制流量和 Site Recovery 通信应保持在 Azure 内部进行。<br/><br/> 解决方法之一是为[这些 IP 范围](#outbound-connectivity-for-azure-site-recovery-ip-ranges)添加用户定义的路由 (UDR)，使流量不流向本地。
**连接** | 如果应用需要连接到本地计算机，或者本地客户端通过 VPN/ExpressRoute 从本地连接到应用，请确保在目标 Azure 区域与本地站点之间至少创建一个[站点到站点连接](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)。<br/><br/> 如果目标 Azure 区域与本地站点之间的流量较高，请在目标区域与本地之间创建另一个 [ExpressRoute 连接](../expressroute/expressroute-introduction.md)。<br/><br/> 若要在故障转移后保留 VM 的 IP，请将目标区域的站点到站点/ExpressRoute 连接保持为断开状态。 这可确保源与目标 IP 地址范围之间没有范围冲突。
**ExpressRoute** | 在源区域和目标区域中创建 ExpressRoute 线路。<br/><br/> 在源网络与 ExpressRoute 线路之间，以及目标网络与线路之间创建连接。<br/><br/> 建议在源区域和目标区域中使用不同的 IP 范围。 线路无法同时连接到多个使用相同 IP 范围的 Azure 网络。<br/><br/> 可以在这两个区域创建使用相同 IP 范围的虚拟网络，然后在这两个区域分别创建 ExpressRoute 线路。 发生故障转移时，断开源虚拟网络中的线路，连接目标虚拟网络中的线路。<br/><br/> 如果主要区域完全关闭，断开连接操作可能会失败。 在这种情况下，目标虚拟网络无法获取 ExpressRoute 连接。
**ExpressRoute 高级版** | 可在同一地缘政治区域创建线路。<br/><br/> 若要在不同的地缘政治区域创建线路，需要使用 Azure ExpressRoute 高级版。<br/><br/> 使用高级版会增加成本。 如果已使用高级版，则不需要额外付费。<br/><br/> 了解有关[位置](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region)和[定价](https://azure.microsoft.com/pricing/details/expressroute/)的详细信息。



## <a name="next-steps"></a>后续步骤

转到[步骤 4：创建保管库](azure-to-azure-walkthrough-vault.md)

