---
title: 如何关闭 Microsoft Azure FXT 边缘文件管理器单元
description: 启动和安全关闭 Azure FXT 边缘筛选器节点的过程
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: v-erkell
ms.openlocfilehash: 6921e7a52e43a63055b59242c02cc6ca3b8c5313
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620097"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>如何安全地关闭 Azure FXT 边缘文件服务器硬件

尽管物理电源按钮可用于切换单个节点上，不应使用要关闭的情况下在正常情况下单元

Azure FXT 边缘筛选器节点作为群集的一部分使用后，应使用群集管理面板软件关闭硬件。 

> [!NOTE] 
> 若要避免可能造成数据丢失或损坏，请始终使用控制面板软件关闭 Azure FXT 边缘筛选器。 不要关闭使用物理电源按钮，除非要求您为此，Microsoft 客户服务和支持。
> 
> 中电源紧急情况下，断开连接电源线连接或使用你的数据中心的电力断开连接的机制。

## <a name="shut-down-a-node-from-the-control-panel"></a>关闭控制面板中的节点

请按照这些说明来安全地关闭 Azure FXT 边缘筛选器节点的电源操作：

1. 登录到群集控制面板。 (在方向[打开设置页](fxt-cluster-create.md#open-the-settings-pages))
1. 单击**设置**选项卡，然后加载**群集** > **FXT 节点**页。
1. 在群集节点的列表中，找到你想要关闭的情况下。 单击**关闭电源**按钮在其**操作**列。 
1. 等待一段时间。 节点将关闭的情况下，关闭电源本身。

## <a name="next-steps"></a>后续步骤

* 了解有关的状态 Led 和中的其他指标[监视器 Azure FXT 边缘文件管理器硬件状态](fxt-monitor.md)。
* 在中提供详细了解 Azure FXT 边缘文件管理器 power[连接电源线](fxt-network-power.md#connect-power-cables)。
