---
title: "将在集成的 Azure 堆栈的系统上的缩放单元节点 |Microsoft 文档"
description: "了解如何将在集成的 Azure 堆栈的系统上的物理缩放单元节点。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: f9434689-ee66-493c-a237-5c81e528e5de
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: mabrigg
ms.openlocfilehash: 4e5b1269e2bee31316cba99d69ea2a6d702faf05
ms.sourcegitcommit: 4256ebfe683b08fedd1a63937328931a5d35b157
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/23/2017
---
# <a name="replace-a-scale-unit-node-on-an-azure-stack-integrated-system"></a>将在集成的 Azure 堆栈的系统上的缩放单元节点

*适用范围： Azure 堆栈集成系统*

本指南介绍了常规进程以替换物理计算机 (也称为*缩放单元节点*) 在 Azure 堆栈上集成系统。 实际的缩放单元节点替换的步骤不同基于原始设备制造商 (OEM) 硬件供应商联系。 请参阅特定于你的系统的详细步骤的供应商的字段可更换部件 (FRU) 文档。

以下流程图显示常规的 FRU 进程，以替换整个缩放单元节点。

![替换节点过程的流程图](media/azure-stack-replace-node/ReplaceNodeFlow.PNG)

* 此操作可能不需要基于硬件的物理条件。

## <a name="review-alert-information"></a>查看警报信息

如果缩放单元节点已关闭，你将收到以下所有严重警报：

- 未连接到网络控制器的节点
- 无法访问虚拟机放置的节点
- 缩放单元节点处于脱机状态

![向下缩放单位的警报的列表](media/azure-stack-replace-node/NodeDownAlerts.PNG)

如果打开"缩放单元节点处于脱机状态的"警报，警报描述包含不可访问的缩放单元节点。 硬件生命周期主机正在运行的特定于 OEM 监视解决方案中，你还可能会收到其他警报。

![节点脱机警报的详细信息](media/azure-stack-replace-node/NodeOffline.PNG)

## <a name="scale-unit-node-replacement-process"></a>缩放单元节点更换过程

以下步骤提供作为缩放单元节点更换过程的高级概述。 请参阅特定于你的系统的详细步骤的 OEM 硬件供应商的 FRU 文档。 不用按照这些步骤而不会引用您 OEM 提供的文档。

1. 使用[排出](azure-stack-node-actions.md#scale-unit-node-actions)操作将缩放单元节点置于维护模式。 此操作可能不需要基于硬件的物理条件。

   > [!NOTE]
   > 在任何情况下，可以完成操作。 只有一个节点且将其关闭在同一时间而不会破坏 SSD (Storage Spaces Direct)。

2. 如果节点仍打开，使用[关闭电源](azure-stack-node-actions.md#scale-unit-node-actions)操作。 此操作可能不需要基于硬件的物理条件。
 
   > [!NOTE]
   > 在关闭操作不起作用不大可能情况下，改用基板管理控制器 (BMC) 的 web 界面。

1. 将物理计算机。 通常情况下，这是通过 OEM 硬件供应商联系。
2. 使用[修复](azure-stack-node-actions.md#scale-unit-node-actions)操作将新的物理计算机添加到缩放单位。
3. 使用到特权的终结点[检查虚拟磁盘修复的状态](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair)。 与新的数据驱动器，完整存储修复作业可能需要数小时，具体取决于系统负载，且使用空间。
4. 修复操作已完成后，验证已自动关闭所有活动警报。

## <a name="next-steps"></a>后续步骤

- 有关替换的热插拔的物理磁盘的信息，请参阅[替换的磁盘](azure-stack-replace-disk.md)。 
- 有关更换非热插拔的硬件组件的信息，请参阅[更换硬件组件](azure-stack-replace-component.md)。 
