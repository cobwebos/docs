---
title: Azure Stack 中的缩放单元节点操作 | Microsoft Docs
description: 了解如何查看节点状态，并使用开机、 关闭、 禁用和恢复 Azure Stack 集成系统上的节点操作。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 12/06/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: ced6e2edb570e12b17d14e0552030902161b5d53
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2018
ms.locfileid: "53725246"
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Azure Stack 中的缩放单元节点操作

*适用于：Azure Stack 集成系统*

本文介绍如何查看缩放单位的状态。 您可以查看单元的节点。 可以电源上运行节点操作，例如 power、 关闭、 清空、 继续和修复。 通常情况下，在现场更换的部件，或若要帮助恢复一个节点期间使用这些节点操作。

> [!Important]  
> 本文中所述的所有节点操作应一次都针对一个节点。

## <a name="view-the-node-status"></a>查看节点状态

在管理员门户中，可以查看缩放单位及其关联的节点的状态。

若要查看缩放单元的状态，请执行以下操作：

1. 在“区域管理”磁贴中选择区域。
2. 在左侧的“基础结构资源”下，选择“缩放单元”。
3. 在结果中选择缩放单元。
4. 在左侧下,**常规**，选择**节点**。

  查看以下信息：

  - 单个节点的列表
  - 操作状态 （请参阅下面的列表）
  - 电源状态 （运行或停止）
  - 服务器模型
  - 基板管理控制器 (BMC) 的 IP 地址
  - 内核总数
  - 总内存量

![缩放单位的状态](media/azure-stack-node-actions/multinodeactions.png)

### <a name="node-operational-states"></a>节点操作状态

| 状态 | 描述 |
|----------------------|-------------------------------------------------------------------|
| 正在运行 | 节点都积极参与缩放单元。 |
| 已停止 | 节点不可用。 |
| 正在添加 | 正在主动将节点添加到缩放单元。 |
| 正在修复 | 正在主动修复节点。 |
| 维护 | 节点已暂停，没有处于运行状态的活动用户工作负荷。 |
| 需要修正 | 检测到错误，需要修复节点。 |

## <a name="scale-unit-node-actions"></a>缩放单元节点操作

查看缩放单元节点的相关信息时，也可以执行节点操作，例如：
 - 启动和停止 （具体取决于当前的电源状态）
 - 禁用和恢复 （具体取决于操作状态）
 - 修复
 - 关机

节点的工作状态确定了哪些选项可用。

你将需要安装 Azure Stack PowerShell 模块。 这些 cmdlet 位于**Azs.Fabric.Admin**模块。 若要安装或验证适用于 Azure Stack 的 PowerShell 安装，请参阅[安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。

## <a name="stop"></a>停止

**停止**操作会关闭节点。 它是相同的如同按下电源按钮。 它不向操作系统发送关机信号。 对于计划的停止操作，始终关闭操作首先尝试。 

当节点处于挂起状态，不再响应请求时，通常使用此操作。

若要运行的停止操作，打开提升的 PowerShell 提示符，并运行以下 cmdlet:

```PowerShell  
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

在罕见的情况下停止操作不起作用，重试该操作并且在出现故障第二次改用 BMC web 界面。

有关详细信息，请参阅[停止 AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/stop-azsscaleunitnode)。

## <a name="start"></a>开始

**启动**操作会在节点上。 它是相同的如同按下电源按钮。 
 
若要运行的启动操作，打开提升的 PowerShell 提示符，并运行以下 cmdlet:

```PowerShell  
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

在罕见的情况下启动操作不起作用，重试该操作并且在出现故障第二次改用 BMC web 界面。

有关详细信息，请参阅[开始 AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/start-azsscaleunitnode)。

## <a name="drain"></a>清空

**清空**操作将所有活动的工作负荷移动到该特定缩放单位中的剩余节点。

在现场更换组件期间（例如，更换整个节点），通常使用此操作。

> [!Important]
> 请确保在计划内的维护时段，已通知用户后，使用节点上的消耗操作。 在某些情况下，活动的工作负荷可能遇到中断。

若要运行清空操作，打开提升的 PowerShell 提示符，并运行以下 cmdlet:

```PowerShell  
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

有关详细信息，请参阅[禁用 AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/disable-azsscaleunitnode)。

## <a name="resume"></a>恢复

**恢复**操作恢复已禁用的节点，并将其标记为活动用于放置工作负荷。 之前在节点上运行的工作负荷不会故障回复。 （如果使用上一个节点的清空操作请务必关闭电源。 重新启动节点时它未标记为活动用于放置工作负荷。 准备就绪后，必须使用恢复操作将节点标记为活动。）

若要运行恢复操作，打开提升的 PowerShell 提示符，并运行以下 cmdlet:

```PowerShell  
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

有关详细信息，请参阅[启用 AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/enable-azsscaleunitnode)。

## <a name="repair"></a>修复

**修复**操作可修复节点。 请只在出现以下情况时才使用此操作：
 - 更换整个节点（不管是否包含新数据磁盘）时
 - 硬件组件发生故障并予以更换之后（如果现场可更换单元 (FRU) 文档中建议更换）。

> [!Important]  
> 当您需要更换节点或单个硬件组件时，请参阅 OEM 硬件供应商的 FRU 文档了解确切的步骤。 FRU 文档将指定在更换硬件组件之后是否需要运行修复操作。 

运行修复操作时，需要指定 BMC IP 地址。 

若要运行修复操作，打开提升的 PowerShell 提示符，并运行以下 cmdlet:

  ````PowerShell
  Repair-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -BMCIPv4Address <BMCIPv4Address>
  ````

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure Stack Fabric 管理员模块，请参阅 [Azs.Fabric.Admin](https://docs.microsoft.com/powershell/module/azs.fabric.admin/?view=azurestackps-1.5.0)。
