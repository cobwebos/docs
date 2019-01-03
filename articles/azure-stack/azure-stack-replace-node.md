---
title: 更换 Azure Stack 集成系统上的缩放单元节点 | Microsoft Docs
description: 了解如何更换 Azure Stack 集成系统上的物理缩放单元节点。
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
ms.date: 12/06/2018
ms.author: mabrigg
ms.openlocfilehash: 9d53aa879c39eb68597a402133a7ff16737f4f65
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2018
ms.locfileid: "53716304"
---
# <a name="replace-a-scale-unit-node-on-an-azure-stack-integrated-system"></a>更换 Azure Stack 集成系统上的缩放单元节点

*适用于：Azure Stack 集成系统*

本文介绍 Azure Stack 集成系统上的一般过程来替换 （也称为缩放单位节点） 的物理计算机。 实际的缩放单元节点更换步骤将因原始设备制造商 (OEM) 硬件供应商而异。 有关你系统特有的详细步骤，请参阅供应商的现场可更换部件 (FRU) 文档。

以下流程图显示更换整个缩放单元节点的一般 FRU 过程。

![更换节点过程的流程图](media/azure-stack-replace-node/replacenodeflow.png)

*根据硬件的物理条件，可能不需要此操作。

> [!Note]  
> 如果关闭操作失败，它被建议使用清空操作之后执行停止操作。 有关更多详细信息，请参阅可用的节点操作  

## <a name="review-alert-information"></a>查看警报信息

如果缩放单位节点已关闭，你将收到以下严重警报：

- 节点未连接到网络控制器
- 无法访问节点以供虚拟机放置
- 缩放单元节点已脱机

![缩放单元节点关闭的警报列表](media/azure-stack-replace-node/nodedownalerts.png)

如果开启“缩放单元节点已脱机”警报，警报说明会包含不可访问的缩放单元节点。 也可能会在硬件生命周期主机上运行的 OEM 特定的监视解决方案中收到其他警报。

![节点脱机警报的详细信息](media/azure-stack-replace-node/nodeoffline.png)

## <a name="scale-unit-node-replacement-process"></a>缩放单元节点更换过程

提供以下步骤作为缩放单元节点更换过程的高级概述。 请参阅 OEM 硬件供应商的 FRU 文档的详细步骤，特定于您的系统。 请勿在未参考 OEM 提供的文档的情况下按照这些步骤操作。

1. 使用**关闭**正常关闭的缩放单位节点操作。 根据硬件的物理条件，可能不需要此操作。 

2. 在可能情况下关闭操作失败，请使用[清空](azure-stack-node-actions.md#drain)操作将缩放单位节点置于维护模式。 根据硬件的物理条件，可能不需要此操作。

   > [!NOTE]  
   > 可以在任何情况下，禁用且不中断 S2D 的情况下关闭一次只有一个节点 （存储空间直通）。

3. 缩放单位节点已处于维护模式后，使用[停止](azure-stack-node-actions.md#stop)操作。 根据硬件的物理条件，可能不需要此操作。

   > [!NOTE]  
   > 在关闭电源操作不起作用的罕见情况下，请改用基板管理控制器 (BMC) Web 界面。

4. 更换物理计算机。 通常，这由 OEM 硬件供应商来完成。
5. 使用[修复](azure-stack-node-actions.md#repair)操作将新的物理计算机添加到缩放单元。
6. 使用到特权终结点[检查虚拟磁盘修复状态](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair)。 利用新的数据驱动器，完整的存储修复作业可能需要数小时的时间，具体取决于系统负载和已使用的空间。
7. 修复操作完成后，验证是否已自动关闭所有活动警报。

## <a name="next-steps"></a>后续步骤

- 有关系统电源上时更换物理磁盘的信息，请参阅[替换的磁盘](azure-stack-replace-disk.md)。 
- 有关更换系统关机的要求的硬件组件的信息，请参阅[更换硬件组件](azure-stack-replace-component.md)。
