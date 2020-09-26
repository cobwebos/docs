---
title: 关于如何使用 Azure Site Recovery 在 Azure VM 灾难恢复中联网
description: 概述了使用 Azure Site Recovery 复制 Azure 虚拟机的网络。
services: site-recovery
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 3/13/2020
ms.author: harshacs
ms.openlocfilehash: db4c3be7c79448e4cf0df39688959ae09a671dbd
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91361409"
---
# <a name="about-networking-in-azure-vm-disaster-recovery"></a>关于如何在 Azure VM 灾难恢复中联网



本文提供了使用 [Azure Site Recovery](site-recovery-overview.md) 在不同区域之间复制和恢复 Azure VM 的网络指南。

## <a name="before-you-start"></a>开始之前

了解 Site Recovery 如何为[此方案](azure-to-azure-architecture.md)提供灾难恢复。

## <a name="typical-network-infrastructure"></a>典型网络基础结构

下图描绘了 Azure VM 上运行的应用程序的典型 Azure 环境：

![客户环境](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

如果使用 Azure ExpressRoute 或从本地网络到 Azure 的 VPN 连接，则环境如下：

![客户环境](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

通常，网络使用防火墙和网络安全组 (NSG) 进行保护。 应使用服务标记来控制网络连接。 Nsg 应允许多个服务标记来控制出站连接。

>[!IMPORTANT]
> Site Recovery 不支持使用经过身份验证的代理控制网络连接，并且无法启用复制。


## <a name="outbound-connectivity-for-urls"></a>URL 的出站连接

如果使用基于 URL 的防火墙代理来控制出站连接，请允许以下 Site Recovery URL：

>[!NOTE]
> 不应执行基于 IP 地址的允许列表来控制出站连接。

**URL** | **详细信息**
--- | ---
\* .blob.core.windows.net | 必需，以便从 VM 将数据写入到源区域中的缓存存储帐户。 如果你知道 Vm 的所有缓存存储帐户，则可以允许访问特定的存储帐户 Url (例如： cache1.blob.core.windows.net 和 cache2.blob.core.windows.net) 而不是 *. blob.core.windows.net
login.microsoftonline.com | 对于 Site Recovery 服务 URL 的授权和身份验证而言是必需的。
*.hypervrecoverymanager.windowsazure.com | 必需，以便从 VM 进行 Site Recovery 服务通信。
*.servicebus.windows.net | 必需，以便从 VM 写入 Site Recovery 监视和诊断数据。
*.vault.azure.net | 允许访问，以便通过门户为支持 ADE 的虚拟机启用复制
*.automation.ext.azure.com | 允许通过门户为复制项启用移动代理自动升级

## <a name="outbound-connectivity-using-service-tags"></a>使用服务标记的出站连接

如果使用 NSG 来控制出站连接，需要允许这些服务标记。

- 对于源区域中的存储帐户：
    - 为源区域创建基于[存储服务标记](../virtual-network/security-overview.md#service-tags)的 NSG 规则。
    - 允许这些地址，才能从 VM 将数据写入到缓存存储帐户。
- 创建一个基于 [Azure Active Directory (AAD) 服务标记](../virtual-network/security-overview.md#service-tags)的 NSG 规则以允许访问与 AAD 对应的所有 IP 地址
- 为目标区域创建基于 EventsHub 服务标记的 NSG 规则，以允许访问 Site Recovery 监视。
- 创建基于 AzureSiteRecovery 服务标记的 NSG 规则，以允许访问任何区域中的 Site Recovery 服务。
- 创建基于 AzureKeyVault 服务标记的 NSG 规则。 仅在通过门户为支持 ADE 的虚拟机启用复制时才需要这样做。
- 创建基于 GuestAndHybridManagement 服务标记的 NSG 规则。 仅在通过门户为复制项启用移动代理自动升级时才需要这样做。
- 在生产 NSG 中创建所需的 NSG 规则之前，建议先在测试 NSG 中创建这些规则，并确保没有任何问题。

## <a name="example-nsg-configuration"></a>NSG 配置示例

此示例演示如何为要复制的 VM 配置 NSG 规则。

- 如果使用 NSG 规则控制出站连接，请对所有必需的 IP 地址范围使用端口 443 的“允许 HTTPS 出站”规则。
- 此示例假设 VM 源位置是“美国东部”，目标位置是“美国中部”。

### <a name="nsg-rules---east-us"></a>NSG 规则 - 美国东部

1. 基于 NSG 规则为“Storage.EastUS”创建出站 HTTPS (443) 安全规则，如以下屏幕截图所示。

      ![屏幕截图显示存储点东 U S 的网络安全组的 "添加出站安全规则"。](./media/azure-to-azure-about-networking/storage-tag.png)

2. 基于 NSG 规则为“AzureActiveDirectory”创建出站 HTTPS (443) 安全规则，如以下屏幕截图所示。

      ![屏幕截图显示了 Azure A 的网络安全组的 "添加出站安全规则"。](./media/azure-to-azure-about-networking/aad-tag.png)

3. 与上述安全规则类似，为 NSG 上的 "CentralUS" 创建出站 HTTPS () 443 安全规则，该规则对应于目标位置。 这样就可以访问 Site Recovery 监视功能。

4. 在 NSG 上为“AzureSiteRecovery”创建出站 HTTPS (443) 安全规则。 这样就可以在任何区域访问 Site Recovery 服务。

### <a name="nsg-rules---central-us"></a>NSG 规则 - 美国中部

必须创建这些规则，才能在故障转移后启用从目标区域到源区域的复制：

1. 基于 NSG 为“Storage.CentralUS”创建出站 HTTPS (443) 安全规则。

2. 基于 NSG 规则为“AzureActiveDirectory”创建出站 HTTPS (443) 安全规则。

3. 与上面的安全规则类似，为与源位置对应的 NSG 上的 "EastUS" 创建出站 HTTPS (443) 安全规则。 这样就可以访问 Site Recovery 监视功能。

4. 在 NSG 上为“AzureSiteRecovery”创建出站 HTTPS (443) 安全规则。 这样就可以在任何区域访问 Site Recovery 服务。

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

对 0.0.0.0/0 地址前缀，可将 Azure 默认系统路由重写为[自定义路由](../virtual-network/virtual-networks-udr-overview.md#custom-routes)，并将 VM 流量转换为本地网络虚拟设备 (NVA)，但不建议对 Site Recovery 复制使用此配置。 如果使用自定义路由，则应在虚拟网络中为“存储”[创建一个虚拟网络服务终结点](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage)，这样复制流量就不会离开 Azure 边界。

## <a name="next-steps"></a>后续步骤
- [复制 Azure 虚拟机](./azure-to-azure-quickstart.md)，开始对工作负荷进行保护。
- 详细了解为 Azure 虚拟机故障转移[保留 IP 地址](site-recovery-retain-ip-azure-vm-failover.md)。
- 详细了解[使用 ExpressRoute 的 Azure 虚拟机](azure-vm-disaster-recovery-with-expressroute.md)的灾难恢复。
