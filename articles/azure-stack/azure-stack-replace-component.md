---
title: "替换的 Azure 堆栈缩放单元节点上的硬件组件 |Microsoft 文档"
description: "了解如何将在集成的 Azure 堆栈的系统上的硬件组件。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: c6e036bf-8c80-48b5-b2d2-aa7390c1b7c9
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: mabrigg
ms.openlocfilehash: 20a830f99c5545b49e7b8c0211d145eef3e46179
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2018
---
# <a name="replace-a-hardware-component-on-an-azure-stack-scale-unit-node"></a>替换的 Azure 堆栈缩放单元节点上的硬件组件

*适用范围： Azure 堆栈集成系统*

本文介绍将替换为非热插拔的硬件组件的一般过程。 步骤会有所不同的实际替换基于原始设备制造商 (OEM) 硬件供应商联系。 请参阅特定于 Azure 堆栈集成系统的详细步骤的供应商的字段可更换部件 (FRU) 文档。

非热插拔组件包括：

- CPU *
- 内存 *
- 母板/基板管理控制器 (BMC) / 视频卡
- 磁盘控制器/主机总线适配器 (HBA) / 底板
- 网络适配器 (NIC)
- 操作系统磁盘 *
- 数据驱动器 （不支持热插拔，例如 PCI e 外接卡的驱动器） *

* 这些组件可能支持热插拔，但供应商实施情况而有所不同。 请参阅 OEM 供应商的 FRU 文档有关的详细步骤。

以下流程图显示常规的 FRU 进程，以替换非热插拔的硬件组件。

![显示组件替换数据流的流关系图](media/azure-stack-replace-component/replacecomponentflow.PNG)

* 此操作可能不需要基于硬件的物理条件。

* * 无法随 OEM 硬件供应商执行组件更换并更新固件的是否在支持协定。

## <a name="review-alert-information"></a>查看警报信息

Azure 堆栈运行状况和监视系统跟踪网络适配器和由存储空间直通控制的数据驱动器的运行状况。 它不跟踪其他硬件组件。 对于所有其他硬件组件，在监视硬件生命周期主机运行的解决方案的特定于供应商的硬件中引发警报。

## <a name="component-replacement-process"></a>组件更换过程

以下步骤提供组件更换过程的高级概述。 不用按照这些步骤而不会引用你的 OEM 提供 FRU 文档。

1. 使用[排出](azure-stack-node-actions.md#scale-unit-node-actions)操作将缩放单元节点置于维护模式。 此操作可能不需要基于硬件的物理条件。
2. 缩放单元节点处于维护模式后，使用[关闭电源](azure-stack-node-actions.md#scale-unit-node-actions)操作。 此操作可能不需要基于硬件的物理条件。
 
   > [!NOTE]
   > 在关闭操作不起作用不大可能情况下，改用基板管理控制器 (BMC) 的 web 界面。

3. 替换损坏的硬件组件。 OEM 硬件供应商是否执行组件更换无法取决于您的支持合同。  
4. 更新固件。 按照你使用硬件生命周期主机用于确保被替换的硬件组件具有批准的固件级别应用的特定于供应商固件更新过程。 OEM 硬件供应商是否执行此步骤可能取决于您的支持合同。  
5. 使用[修复](azure-stack-node-actions.md#scale-unit-node-actions)操作以将缩放单元节点恢复到的缩放单位。
6. 使用到特权的终结点[检查虚拟磁盘修复的状态](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair)。 与新的数据驱动器，完整存储修复作业可能需要数小时，具体取决于系统负载，且使用空间。
7. 修复操作已完成后，验证已自动关闭所有活动警报。

## <a name="next-steps"></a>后续步骤

- 有关替换的热插拔的物理磁盘的信息，请参阅[替换的磁盘](azure-stack-replace-disk.md)。
- 有关替换物理节点的信息，请参阅[替换缩放单元节点](azure-stack-replace-node.md)。
- 