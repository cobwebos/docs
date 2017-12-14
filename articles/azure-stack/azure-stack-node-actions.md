---
title: "缩放 Azure 堆栈中的单元节点操作 |Microsoft 文档"
description: "了解如何查看节点状态和使用开机、 关机，排出，并恢复节点在集成的 Azure 堆栈的系统上的操作。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: dbb68b10-c721-4188-aa07-584d0cd63138
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: mabrigg
ms.openlocfilehash: 4b94092f1284abfa2462ddef04b6e84136e54dde
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Azure 堆栈中的缩放单元节点操作

*适用范围： Azure 堆栈集成系统*

本文介绍如何查看缩放单位和其关联的节点的状态以及如何使用的可用节点操作。 节点的操作包括开机电源关闭、 排出、 继续和修复。 通常情况下，字段更换部件，或节点恢复方案的过程中使用这些节点的操作。

## <a name="view-the-status-of-a-scale-unit-and-its-nodes"></a>查看缩放单位和及其节点的状态

在管理员门户中，你可以轻松地查看缩放单位和其关联的节点的状态。

若要查看扩展单位的状态：

1. 上**区域管理**磁贴中，选择的区域。
2. 在左侧下,**基础结构资源**，选择**缩放单元**。
3. 在结果中，选择的缩放单位。
 
在这里，你可以查看以下信息：

- 区域名称
- 系统类型
- 总逻辑内核数
- 总内存
- 各个节点和它们的状态; 的列表请运行或停止。

![缩放单元磁贴显示每个节点的运行状态](media/azure-stack-node-actions/ScaleUnitStatus.PNG)

## <a name="view-information-about-a-scale-unit-node"></a>查看有关缩放单元节点信息

如果你选择的单个节点，你可以查看以下信息：

- 区域名称
- 服务器模型
- 基板管理控制器 (BMC) 的 IP 地址
- 操作状态
- 总内核数
- 内存总量
 
![缩放单元磁贴显示每个节点的运行状态](media/azure-stack-node-actions/NodeActions.PNG)

你还可以从此处执行缩放单元节点操作。

## <a name="scale-unit-node-actions"></a>缩放单元节点操作

当你查看有关缩放单元节点的信息时，你还可以如执行节点操作：

- 开机和关机
- 漏和恢复
- 修复

节点的操作的状态确定了可用的选项。

### <a name="power-off"></a>关闭电源

**关闭电源**操作将关闭节点。 它是相同的就像你按下电源按钮。 与其**不**向操作系统发送关闭信号。 对于计划关闭操作，请确保首先排出缩放单元节点。

当节点处于挂起状态且不再响应请求，通常使用此操作。  

若要运行关闭通过 PowerShell 的操作：

  ````PowerShell
  Stop-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
  ```` 

在可能的情况下，关闭操作不起作用，请改用 BMC web 界面。

### <a name="power-on"></a>开机

**开启**操作会将在节点上。 它是相同的就像你按下电源按钮。 

对操作通过 PowerShell 运行幂：

  ````PowerShell
  Start-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
  ````

在可能的情况下，在开启操作不起作用，请改用 BMC web 界面。

### <a name="drain"></a>清空

**排出**操作 evacuates 方法是将它们分布在该特定缩放单位中的剩余节点之间的所有活动的工作负荷。

字段的部件，例如整个节点替换的更换过程中通常使用此操作。

> [!IMPORTANT]
> 请确保你仅在计划的维护窗口，其中用户已接到通知期间排出节点。 在某些情况下活动的工作负荷可能会遇到中断。

若要运行通过 PowerShell 排出操作：

  ````PowerShell
  Disable-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
  ````

### <a name="resume"></a>恢复

**恢复**操作恢复已用完的节点，并将其标记为活动状态的工作负荷放置。 早期节点运行的工作负荷不会返回失败。 （如果你排出节点，然后关机，重新打开电源节点时，它未标记为活动状态工作负载放置。 准备就绪时，你必须用于恢复操作将标记为活动节点。）

若要运行通过 PowerShell 恢复操作：

  ````PowerShell
  Enable-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
  ````

### <a name="repair"></a>修复

**修复**操作修复节点。 它仅用于以下方案之一：

- 完整节点替换 （有或没有新数据磁盘）
- 后硬件组件故障和替换 （如果建议字段可更换部件 (FRU) 文档中）。

> [!IMPORTANT]
> 当你需要替换的节点或单独的硬件组件时，请参阅具体的步骤的 OEM 硬件供应商的 FRU 文档。 FRU 文档将指定是否需要运行替换的硬件组件后修复操作。  

运行修复操作时，你需要指定的 BMC IP 地址。 

若要运行通过 PowerShell 修复操作：

  ````PowerShell
  Repair-AzsScaleUnitNode -Region <RegionName> -Name <NodeName> -BMCIPAddress <BMCIPAddress>
  ````


