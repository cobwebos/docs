---
title: 使用 Azure 门户部署 Azure 专用主机 |Microsoft Docs
description: 使用 Azure 门户将 Vm 部署到专用主机。
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/26/2019
ms.author: cynthn
ms.openlocfilehash: 0a3cb76d1e28559727957c1007b0992fb8e90f2b
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68700556"
---
# <a name="preview-deploy-vms-to-dedicated-hosts-using-the-portal"></a>预览版：使用门户将 Vm 部署到专用主机

本文介绍如何创建 Azure[专用主机](dedicated-hosts.md)来托管虚拟机 (vm)。 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>创建 VM

1. 在 Azure 门户的左上角选择“创建资源”。
1. 在“新建”页的“常用”下，选择“Windows Server 2016 Datacenter”。
1. 在 "**基本**信息" 选项卡中的 "**项目详细信息**" 下, 确保选择了正确的订阅, 然后选择 " *myDedicatedHostsRG* " 作为**资源组**。 
1. 在“实例详细信息”下，对于“虚拟机名称”键入 *myVM*，对于“位置”选择“美国东部”。
1. 在 "**可用性选项**" 中, 选择 "**可用性区域**", 然后从下拉范围中选择*1* 。
1. 对于 "大小", 请选择 "**更改大小**"。 在可用大小列表中, 选择 Esv3 系列中的一个, 如**标准 E2s v3**。 您可能需要清除筛选器才能查看所有可用大小。
1. 在“管理员帐户”下，提供用户名（例如 *azureuser*）和密码。 密码必须至少 12 个字符长，且符合[定义的复杂性要求](faq.md#what-are-the-password-requirements-when-creating-a-vm)。
1. 在“入站端口规则”下，选择“允许所选端口”，然后从下拉列表中选择“RDP (3389)”。
1. 在页面顶部, 选择 "**高级**" 选项卡, 然后在 "**主机**" 部分中, 选择 " *myHostGroup* "**作为 "** **主机组**" 和 " *myhost 代表*"。 
    ![选择主机组和主机](./media/dedicated-hosts-portal/advanced.png)
1. 保留其余默认值，然后选择页面底部的“查看 + 创建”按钮。
1. 看到验证已通过的消息后, 选择 "**创建**"。


## <a name="next-steps"></a>后续步骤

- 有关详细信息, 请参阅[专用主机](dedicated-hosts.md)概述。 

- [这里](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)有一个示例模板, 它使用区域和容错域实现了区域中的最大复原能力。

- 你还可以使用[Azure PowerShell](dedicated-hosts-powershell.md)部署专用主机。