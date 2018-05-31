---
title: 关于使用 Azure Site Recovery 进行 Azure 到 Azure 灾难恢复的网络 | Microsoft Docs
description: 概述了使用 Azure Site Recovery 复制 Azure 虚拟机的网络。
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/17/2018
ms.author: sujayt
ms.openlocfilehash: e3acedf4135166f5239b95eb21eb5dfd66d6100f
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2018
ms.locfileid: "32312621"
---
# <a name="about-networking-in-azure-to-azure-replication"></a>关于 Azure 到 Azure 复制的网络

>[!NOTE]
> 用于 Azure 虚拟机的 Site Recovery 复制当前处于预览状态。

本文提供了使用 [Azure Site Recovery](site-recovery-overview.md) 在不同区域之间复制和恢复 Azure VM 的网络指南。

## <a name="before-you-start"></a>开始之前

了解 Site Recovery 如何为[此方案](azure-to-azure-architecture.md)提供灾难恢复。

## <a name="typical-network-infrastructure"></a>典型网络基础结构

下图描绘了 Azure VM 上运行的应用程序的典型 Azure 环境：

![customer-environment](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

如果使用 Azure ExpressRoute 或从本地网络到 Azure 的 VPN 连接，则环境如下所示：

![customer-environment](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

通常，网络使用防火墙和网络安全组 (NSG) 进行保护。 防火墙使用基于 URL 或 IP 的允许列表来控制网络连接。 NSG 提供使用 IP 地址范围控制网络连接的规则。

>[!IMPORTANT]
> Site Recovery 不支持使用经过身份验证的代理控制网络连接，并且无法启用复制。


## <a name="outbound-connectivity-for-urls"></a>URL 的出站连接

如果使用基于 URL 的防火墙代理来控制出站连接，请允许以下 Site Recovery URL：


**URL** | **详细信息**  
--- | ---
* .blob.core.windows.net | 必需，以便从 VM 将数据写入到源区域中的缓存存储帐户。
login.microsoftonline.com | 必需，用于向 Site Recovery 服务 URL 进行授权和身份验证。
*.hypervrecoverymanager.windowsazure.com | 必需，以便从 VM 进行 Site Recovery 服务通信。
* .servicebus.windows.net | 必需，以便从 VM 写入 Site Recovery 监视和诊断数据。

## <a name="outbound-connectivity-for-ip-address-ranges"></a>IP 地址范围的出站连接

如果使用基于 IP 的防火墙代理或 NSG 规则来控制出站连接，需要允许这些 IP 地址范围。

- 对应于源区域中存储帐户的所有 IP 地址范围
    - 为源区域创建基于[存储服务标记](../virtual-network/security-overview.md#service-tags)的 NSG 规则。
    - 允许这些地址，才能从 VM 将数据写入到缓存存储帐户。
- 对应于 Office 365 [身份验证和标识 IP V4 终结点](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)的所有 IP 地址范围。
    - 如果将来向 Office 365 范围添加新地址，则需要创建新的 NSG 规则。
- Site Recovery 服务终结点 IP 地址（[以 XML 文件形式提供](https://aka.ms/site-recovery-public-ips)，具体取决于目标位置）。
-  可以[下载并使用此脚本](https://aka.ms/nsg-rule-script)，以在 NSG 上自动创建所需的规则。
- 在生产 NSG 中创建所需的 NSG 规则之前，建议先在测试 NSG 中创建这些规则，并确保没有任何问题。


Site Recovery IP 地址范围如下：

   **目标** | **Site Recovery IP** |  **Site Recovery 监视 IP**
   --- | --- | ---
   东亚 | 52.175.17.132 | 13.94.47.61
   东南亚 | 52.187.58.193 | 13.76.179.223
   印度中部 | 52.172.187.37 | 104.211.98.185
   印度南部 | 52.172.46.220 | 104.211.224.190
   美国中北部 | 23.96.195.247 | 168.62.249.226
   北欧 | 40.69.212.238 | 52.169.18.8
   欧洲西部 | 52.166.13.64 | 40.68.93.145
   美国东部 | 13.82.88.226 | 104.45.147.24
   美国西部 | 40.83.179.48 | 104.40.26.199
   美国中南部 | 13.84.148.14 | 104.210.146.250
   美国中部 | 40.69.144.231 | 52.165.34.144
   美国东部 2 | 52.184.158.163 | 40.79.44.59
   日本东部 | 52.185.150.140 | 138.91.1.105
   日本西部 | 52.175.146.69 | 138.91.17.38
   巴西南部 | 191.234.185.172 | 23.97.97.36
   澳大利亚东部 | 104.210.113.114 | 191.239.64.144
   澳大利亚东南部 | 13.70.159.158 | 191.239.160.45
   加拿大中部 | 52.228.36.192 | 40.85.226.62
   加拿大东部 | 52.229.125.98 | 40.86.225.142
   美国中西部 | 52.161.20.168 | 13.78.149.209
   美国西部 2 | 52.183.45.166 | 13.66.228.204
   英国西部 | 51.141.3.203 | 51.141.14.113
   英国南部 | 51.140.43.158 | 51.140.189.52
   英国南部 2 | 13.87.37.4| 13.87.34.139
   英国北部 | 51.142.209.167 | 13.87.102.68
   韩国中部 | 52.231.28.253 | 52.231.32.85
   韩国南部 | 52.231.298.185 | 52.231.200.144
   法国中部 | 52.143.138.106 | 52.143.136.55
   法国南部 | 52.136.139.227 |52.136.136.62


## <a name="example-nsg-configuration"></a>NSG 配置示例

此示例演示如何为要复制的 VM 配置 NSG 规则。

- 如果使用 NSG 规则控制出站连接，请对所有必需的 IP 地址范围使用端口 443 的“允许 HTTPS 出站”规则。
- 此示例假设 VM 源位置是“美国东部”，目标位置是“美国中部”。

### <a name="nsg-rules---east-us"></a>NSG 规则 - 美国东部

1. 基于 NSG 规则为“Storage.EastUS”创建出站 HTTPS (443) 安全规则，如以下屏幕截图所示。

      ![storage-tag](./media/azure-to-azure-about-networking/storage-tag.png)

2. 为对应于 Office 365 [身份验证和标识 IP V4 终结点](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)的所有 IP 地址范围创建出站 HTTPS (443) 规则。
3. 为对应于目标位置的 Site Recovery IP 创建出站 HTTPS (443) 规则：

   **位置** | **Site Recovery IP 地址** |  **Site Recovery 监视 IP 地址**
    --- | --- | ---
   美国中部 | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>NSG 规则 - 美国中部

必须创建这些规则，才能在故障转移后启用从目标区域到源区域的复制：

1. 基于 NSG 为“Storage.CentralUS”创建出站 HTTPS (443) 安全规则。

2. 为对应于 Office 365 [身份验证和标识 IP V4 终结点](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)的所有 IP 地址范围创建出站 HTTPS (443) 规则。

3. 为对应于源位置的 Site Recovery IP 创建出站 HTTPS (443) 规则：

   **位置** | **Site Recovery IP 地址** |  **Site Recovery 监视 IP 地址**
    --- | --- | ---
   美国中部 | 13.82.88.226 | 104.45.147.24

## <a name="network-virtual-appliance-configuration"></a>网络虚拟设备配置

如果使用网络虚拟设备 (NVA) 控制来自 VM 的出站网络流量，则设备可能在所有复制流量通过 NVA 的情况下受到限制。 我们建议在虚拟网络中为“存储”创建一个网络服务终结点，这样复制流量就不会经过 NVA。

### <a name="create-network-service-endpoint-for-storage"></a>为存储创建网络服务终结点
可在虚拟网络中为“存储”创建一个网络服务终结点，这样复制流量就不会离开 Azure 边界。

- 选择 Azure 虚拟网络并单击“服务终结点”。

    ![storage-endpoint](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- 单击“添加”，“添加服务终结点”选项卡随即打开
- 选择“服务”下的“Microsoft.Storage”和“子网”字段下的所需子网，并单击“添加”。

>[!NOTE]
>不限制虚拟网络对用于 ASR 的存储帐户的访问权限。 应允许来自“所有网络”的访问

### <a name="forced-tunneling"></a>强制隧道

对 0.0.0.0/0 地址前缀，可将 Azure 默认系统路由重写为[自定义路由](../virtual-network/virtual-networks-udr-overview.md#custom-routes)，并将 VM 流量转换为本地网络虚拟设备 (NVA)，但不建议对 Site Recovery 复制使用此配置。 如果使用自定义路由，应在虚拟网络中为“存储”[创建一个虚拟网络服务终结点](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage)，这样复制流量就不会离开 Azure 边界。

## <a name="next-steps"></a>后续步骤
- [复制 Azure 虚拟机](site-recovery-azure-to-azure.md)，开始对工作负荷进行保护。
- 详细了解为 Azure 虚拟机故障转移[保留 IP 地址](site-recovery-retain-ip-azure-vm-failover.md)。
- 详细了解[使用 ExpressRoute 的 Azure 虚拟机](azure-vm-disaster-recovery-with-expressroute.md)的灾难恢复。
