---
title: 如何关闭微软 Azure FXT 边缘文件器单元
description: 启动和安全关闭 Azure FXT 边缘文件器节点的过程
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 91445c4219abaa67385105f52515f52c87e2cf06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "72256001"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>如何安全地关闭 Azure FXT 边缘文件程序硬件

尽管可以使用物理电源按钮打开单个节点，但在正常情况下，不应使用它关闭设备。

在 Azure FXT 边缘文件程序节点作为群集的一部分使用后，应使用群集控制面板软件关闭硬件。 

> [!NOTE] 
> 为了避免可能的数据丢失或损坏，请始终使用控制面板软件关闭 Azure FXT 边缘文件器。 除非 Microsoft 客户服务和支持部门指示您关闭，否则请勿使用物理电源按钮关闭。
> 
> 在紧急情况下，断开电源线或使用数据中心的断电机制。

## <a name="shut-down-a-node-from-the-control-panel"></a>从控制面板关闭节点

按照以下说明安全地关闭 Azure FXT 边缘文件器节点：

1. 登录到群集控制面板。 （[打开"设置"页时](fxt-cluster-create.md#open-the-settings-pages)的说明）
1. 单击 **"设置"** 选项卡，然后加载**群集** > **FXT 节点**页面。
1. 在群集节点列表中，找到要关闭的节点。 单击其 **"操作"** 列中的 **"断电**"按钮。 
1. 等一下 节点将关闭并自行关闭电源。

## <a name="next-steps"></a>后续步骤

* 了解[监视器 Azure FXT 边缘文件程序硬件状态](fxt-monitor.md)中的状态 LED 和其他指标。
* 阅读有关[连接电源线](fxt-network-power.md#connect-power-cables)中的 Azure FXT 边缘文件器电源的更多内容。
