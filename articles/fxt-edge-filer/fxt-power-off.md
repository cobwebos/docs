---
title: 如何关闭 Microsoft Azure FXT Edge 文件服务器单元
description: Azure FXT Edge 文件服务器节点的启动和安全关闭过程
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 91445c4219abaa67385105f52515f52c87e2cf06
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256001"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>如何安全关闭 Azure FXT Edge 文件服务器硬件

尽管可以使用 "物理电源" 按钮来切换单个节点，但在正常情况下，不应使用它来关闭单元。

将 Azure FXT Edge 文件服务器节点作为群集的一部分使用后，应使用群集控制面板软件关闭硬件。 

> [!NOTE] 
> 若要避免可能的数据丢失或损坏，请始终使用控制面板软件关闭 Azure FXT Edge 文件服务器。 除非 Microsoft 客户服务和支持人员指示你执行此操作，否则不要使用 "物理电源" 按钮进行关机。
> 
> 在电气紧急情况下，拔下电源线，或使用您的数据中心的断开连接机制。

## <a name="shut-down-a-node-from-the-control-panel"></a>从控制面板关闭节点

按照以下说明安全关闭 Azure FXT Edge 文件服务器节点：

1. 登录到 "群集控制面板"。 （[打开 "设置" 页中的](fxt-cluster-create.md#open-the-settings-pages)方向）
1. 单击 "**设置**" 选项卡，然后加载**群集** > **FXT 节点**"页。
1. 在群集节点列表中，找到要关闭的节点。 单击 "**操作**" 列中的 "**关闭**" 按钮。 
1. 稍等片刻。 节点将关闭并关机。

## <a name="next-steps"></a>后续步骤

* 了解[监视 AZURE FXT Edge 文件服务器硬件状态](fxt-monitor.md)中的状态 led 和其他指标。
* 详细了解如何[连接电源线](fxt-network-power.md#connect-power-cables)中的 Azure FXT Edge 文件服务器电源。
