---
title: 因为没有在 NSG 中启用 RDP 端口而无法连接到 Azure VM | Microsoft Docs
description: 了解如何在 Azure 门户中解决 RDP 因为 NSG 配置而失败的问题 | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: cb9058d4f68b2dc202edeeaa6cafb2eefa82470b
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2018
ms.locfileid: "52284721"
---
#  <a name="cannot-connect-remotely-to-a-vm-because-rdp-port-is-not-enabled-in-nsg"></a>因为没有在 NSG 中启用 RDP 端口而无法远程连接到 VM

本文解释了如何解决因为没有在网络安全组 (NSG) 中启用远程桌面协议 (RDP) 端口而无法连接到 Azure Windows 虚拟机 (VM) 的问题。


> [!NOTE] 
> Azure 有两种用于创建和使用资源的部署模型：[Resource Manager 部署模型和经典部署模型](../../azure-resource-manager/resource-manager-deployment-model.md)。 对于新部署，建议你使用资源管理器部署模型而非经典部署模型。 

## <a name="symptom"></a>症状

无法建立到 Azure 中 VM 的 RDP 连接，因为没有在网络安全组中打开 RDP 端口。

## <a name="solution"></a>解决方案 

创建新的 VM 时，默认情况下会阻止来自 Internet 的所有流量。 

若要在 NSG 中启用 RDP 端口，请执行以下步骤：
1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在“虚拟机”中，选择有问题的 VM。 
3. 在“设置”中，选择“网络”。 
4. 在“入站端口规则”中，检查是否正确设置了 RDP 的端口。 下面是一个配置示例： 

    **优先级**：300 </br>
    **端口**：3389 </br>
    **名称**：Port_3389 </br>
    **端口**：3389 </br>
    **协议**：TCP </br>
    **源**：任何 </br>
    **目标**：任何 </br>
    **操作**：允许 </br>

如果你指定了源 IP 地址，则此设置仅允许来自特定 IP 地址或 IP 地址范围的流量连接到 VM。 请确保你用来启动 RDP 会话的计算机在该范围内。

有关 NSG 的详细信息，请参阅[网络安全组](../../virtual-network/security-overview.md)。

> [!NOTE]
> RDP 端口 3389 公开给 Internet。 因此，我们建议你仅将此端口用于测试。 对于生产环境，建议使用 VPN 或专用连接。

## <a name="next-steps"></a>后续步骤

如果已在 NSG 中启用了 RDP 端口，请参阅[解决 Azure VM 中的 RDP 一般错误](./troubleshoot-rdp-general-error.md)。



