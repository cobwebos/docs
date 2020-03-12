---
title: 使用门户部署 Azure 专用主机
description: 使用门户将 Vm 部署到专用主机。
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: cynthn
ms.openlocfilehash: b6f5e155b76535c4d9e0080983d5f54cec3adb01
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086943"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-portal"></a>使用门户将 Vm 部署到专用主机

本文介绍如何创建 Azure[专用主机](dedicated-hosts.md)来托管虚拟机（vm）。 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>创建 VM

1. 在 Azure 门户的左上角选择“创建资源”。
1. 在“新建”页的“常用”下，选择“Windows Server 2016 Datacenter”。
1. 在 "**基本**信息" 选项卡中的 "**项目详细信息**" 下，确保选择了正确的订阅，然后选择 " *myDedicatedHostsRG* " 作为**资源组**。 
1. 在“实例详细信息”下，对于“虚拟机名称”键入 **myVM**，对于“位置”选择“美国东部”。
1. 在 "**可用性选项**" 中，选择 "**可用性区域**"，然后从下拉范围中选择*1* 。
1. 对于 "大小"，请选择 "**更改大小**"。 在可用大小列表中，选择 Esv3 系列中的一个，如**标准 E2s v3**。 您可能需要清除筛选器才能查看所有可用大小。
1. 在“管理员帐户”下，提供用户名（例如 *azureuser*）和密码。 密码必须至少 12 个字符长，且符合[定义的复杂性要求](faq.md#what-are-the-password-requirements-when-creating-a-vm)。
1. 在“入站端口规则”下，选择“允许所选端口”，然后从下拉列表中选择“RDP (3389)”。
1. 在页面顶部，选择 "**高级**" 选项卡，然后在 "**主机**" 部分中，选择 " *myHostGroup* "**作为 "** **主机组**" 和 " *myhost 代表*"。 
    ![选择主机组和主机](./media/dedicated-hosts-portal/advanced.png)
1. 保留其余默认值，然后选择页面底部的“查看 + 创建”按钮。
1. 看到验证已通过的消息后，选择 "**创建**"。

## <a name="add-an-existing-vm"></a>添加现有 VM 

可以将现有 VM 添加到专用主机，但必须先 Stop\Deallocated. VM 将 VM 移到专用主机之前，请确保支持 VM 配置：

- VM 大小必须与专用主机在同一大小系列中。 例如，如果专用主机是 DSv3，则可以 Standard_D4s_v3 VM 大小，但不能是 Standard_A4_v2。 
- VM 需要与专用主机位于同一区域。
- VM 不能是邻近位置组的一部分。 将 VM 移到专用主机之前，请先从邻近感应放置组中将其删除。 有关详细信息，请参阅[将 VM 移出邻近位置组](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#move-an-existing-vm-out-of-a-proximity-placement-group)
- VM 不能位于可用性集中。
- 如果 VM 位于可用性区域中，则它必须是与主机组相同的可用性区域。 VM 和主机组的可用性区域设置必须匹配。

使用[门户](https://portal.azure.com)将 VM 移到专用主机。

1. 打开 VM 的页面。
1. 选择 "**停止**" 以 stop\deallocate VM。
1. 从左侧菜单中选择 "**配置**"。
1. 从下拉菜单中选择主机组和主机。
1. 完成后，请选择页面顶部的 "**保存**"。
1. 将 VM 添加到主机后，从左侧菜单中选择 "**概述**"。
1. 在页面顶部，选择 "**启动**" 以重新启动 VM。

## <a name="next-steps"></a>后续步骤

- 有关详细信息，请参阅[专用主机](dedicated-hosts.md)概述。 

- [这里](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)有一个示例模板，它使用区域和容错域实现了区域中的最大复原能力。

- 你还可以使用[Azure PowerShell](dedicated-hosts-powershell.md)部署专用主机。
