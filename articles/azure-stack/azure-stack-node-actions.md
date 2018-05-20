---
title: Azure Stack 中的缩放单位节点操作 | Microsoft Docs
description: 了解如何在 Azure Stack 集成系统中查看节点状态，以及使用开机、关机、清空和恢复节点操作。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.openlocfilehash: 202854157dee28f3ab3dc73c6f22508a8bf510b3
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2018
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Azure Stack 中的缩放单位节点操作

*适用于：Azure Stack 集成系统*

本文介绍如何查看缩放单位及其关联节点的状态，以及如何使用可用的节点操作。 节点操作包括开机、关机、清空、恢复和修复。 通常，在现场更换组件期间或者在节点恢复场合下会使用这些节点操作。

> [!Important]  
> 本文中所述的所有节点操作每次应该针对一个节点。


## <a name="view-the-status-of-a-scale-unit-and-its-nodes"></a>查看缩放单位及其节点的状态

在管理员门户中，可以轻松查看缩放单位及其关联节点的状态。

查看缩放单位的状态：

1. 在“区域管理”磁贴中选择区域。
2. 在左侧的“基础结构资源”下，选择“缩放单位”。
3. 在结果中选择缩放单位。
 
可在此处查看以下信息：

- 区域名称
- 系统类型
- 逻辑核心总数
- 总内存量
- 各个节点的列表及其状态：“正在运行”或“已停止”。

![显示每个节点运行状态的缩放单位磁贴](media/azure-stack-node-actions/ScaleUnitStatus.PNG)

## <a name="view-information-about-a-scale-unit-node"></a>查看缩放单位节点的相关信息

如果选择单个节点，可以查看以下信息：

- 区域名称
- 服务器模型
- 基板管理控制器 (BMC) 的 IP 地址
- 工作状态
- 内核总数
- 总内存量
 
![显示每个节点运行状态的缩放单位磁贴](media/azure-stack-node-actions/NodeActions.PNG)

还可以在此处执行缩放单位节点操作。

## <a name="scale-unit-node-actions"></a>缩放单位节点操作

查看缩放单位节点的相关信息时，也可以执行节点操作，例如：

- 开机和关机
- 清空和恢复
- 修复

节点的工作状态确定了哪些选项可用。

### <a name="power-off"></a>关机

“关机”操作会关闭节点。 它的作用如同按下电源按钮。 它**不会**向操作系统发送关机信号。 执行计划的关机操作之前，请确保先清空缩放单位节点。

当节点处于挂起状态，不再响应请求时，通常使用此操作。

> [!Important] 
> 只能通过 PowerShell 使用此功能。 今后会在 Azure Stack 管理员门户中再次提供此功能。


通过 PowerShell 运行关机操作：

````PowerShell
  Stop-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
```` 

在关机操作不起作用的罕见情况下，请改用 BMC Web 界面。

### <a name="power-on"></a>开机

“开机”操作会打开节点。 它的作用如同按下电源按钮。 

> [!Important] 
> 只能通过 PowerShell 使用此功能。 今后会在 Azure Stack 管理员门户中再次提供此功能。

通过 PowerShell 运行开机操作：

````PowerShell
  Start-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
````

在开机操作不起作用的罕见情况下，请改用 BMC Web 界面。

### <a name="drain"></a>清空

“清空”操作会逐出所有活动的工作负荷：将它们分配到该特定缩放单位的剩余节点上。

在现场更换组件期间（例如，更换整个节点），通常使用此操作。

> [!IMPORTANT]
> 确保只在已通知用户后执行计划内维护期间才清空节点。 在某些情况下，活动的工作负荷可能遇到中断。

通过 PowerShell 运行清空操作：

  ````PowerShell
  Disable-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
  ````

### <a name="resume"></a>恢复

“恢复”操作可恢复已清空的节点，并将其标记为活动，可用于放置工作负荷。 之前在节点上运行的工作负荷不会故障回复。 （如果清空节点然后关机，在重新打开节点时，系统不会将它标记为活动，可用于放置工作负荷。 准备就绪后，必须使用恢复操作将节点标记为活动。）

通过 PowerShell 运行恢复操作：

  ````PowerShell
  Enable-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
  ````

### <a name="repair"></a>修复

“修复”操作可修复节点。 请只在出现以下情况时才使用此操作：

- 更换整个节点（不管是否包含新数据磁盘）时
- 硬件组件发生故障并予以更换之后（如果现场可更换单元 (FRU) 文档中建议更换）。

> [!IMPORTANT]
> 需要更换节点或单个硬件组件时，请参阅 OEM 硬件供应商的 FRU 文档，以了解确切的步骤。 FRU 文档将指定在更换硬件组件之后是否需要运行修复操作。  

运行修复操作时，需要指定 BMC IP 地址。 

通过 PowerShell 运行修复操作：

  ````PowerShell
  Repair-AzsScaleUnitNode -Region <RegionName> -Name <NodeName> -BMCIPAddress <BMCIPAddress>
  ````


