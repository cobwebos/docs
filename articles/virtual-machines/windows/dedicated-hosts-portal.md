---
title: 使用门户部署 Azure 专用主机
description: 使用门户将 VM 部署到专用主机。
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 53a9cd33f7bd2223be1c9f199b7a6d10d876c322
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082928"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-portal"></a>使用门户将 VM 部署到专用主机

本文将指导您如何创建 Azure[专用主机](dedicated-hosts.md)来托管虚拟机 （VM）。 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>创建 VM

1. 在 Azure 门户的左上角选择“创建资源”。****
1. 在“新建”**** 页的“常用”**** 下，选择“Windows Server 2016 Datacenter”****。
1. 在 **"基本"** 选项卡中，**在"项目详细信息**"下，请确保选择了正确的订阅，然后选择*my 专用主机RG*作为**资源组**。 
1. 在“实例详细信息”**** 下，对于“虚拟机名称”**** 键入 *myVM*，对于“位置”**** 选择“美国东部”**。
1. 在 **"可用性"选项**中，选择 **"可用性区域**"，从下拉列表中选择*1。*
1. 对于大小，选择 **"更改大小**"。 在可用尺寸列表中，从 Esv3 系列中选择一个，如**标准 E2s v3**。 您可能需要清除筛选器才能查看所有可用大小。
1. 在“管理员帐户”  下，提供用户名（例如 *azureuser*）和密码。 密码必须至少 12 个字符长，且符合[定义的复杂性要求](faq.md#what-are-the-password-requirements-when-creating-a-vm)。
1. 在“入站端口规则”下，选择“允许所选端口”，然后从下拉列表中选择“RDP (3389)”。************
1. 在页面顶部，选择 **"高级**"选项卡，在 **"主机**"部分中，为**主机组**选择*myHostGroup，* 为 **"主机**"选择*myHost*组。 
    ![选择主机组和主机](./media/dedicated-hosts-portal/advanced.png)
1. 保留其余默认值，然后选择页面底部的“查看 + 创建”  按钮。
1. 当您看到验证已通过的消息时，选择 **"创建**"。

## <a name="add-an-existing-vm"></a>添加现有 VM 

您可以将现有 VM 添加到专用主机，但 VM 必须首先处于"停止_交易"的位置。 在将 VM 移动到专用主机之前，请确保 VM 配置受支持：

- VM 大小必须与专用主机的大小相同。 例如，如果您的专用主机是 DSv3，则 VM 大小可以Standard_D4s_v3，但它不可能是Standard_A4_v2。 
- VM 需要与专用主机位于同一区域。
- VM 不能是邻近放置组的一部分。 在将 VM 移动到专用主机之前，请从接近放置组中删除 VM。 有关详细信息，请参阅将[VM 移出邻近放置组](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#move-an-existing-vm-out-of-a-proximity-placement-group)
- VM 不能位于可用性集中。
- 如果 VM 位于可用性区域中，则它必须与主机组位于同一可用性区域。 VM 和主机组的可用性区域设置必须匹配。

使用[门户](https://portal.azure.com)将 VM 移动到专用主机。

1. 打开 VM 的页面。
1. 选择 **"停止"** 以停止\取消分配 VM。
1. 从左侧菜单中选择 **"配置**"。
1. 从下拉菜单中选择主机组和主机。
1. 完成后，选择"在页面顶部**保存**"。
1. 将 VM 添加到主机后，从左侧菜单中选择 **"概述**"。
1. 在页面顶部，选择 **"开始"** 以重新启动 VM。

## <a name="next-steps"></a>后续步骤

- 有关详细信息，请参阅[专用主机](dedicated-hosts.md)概述。 

- [此处](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)找到的示例模板，它同时使用区域和容错域，以实现区域中的最大恢复能力。

- 您还可以使用[Azure PowerShell](dedicated-hosts-powershell.md)部署专用主机。
