---
title: "Azure 网络观察程序中的拓扑简介 | Microsoft 文档"
description: "此页概述了网络观察程序拓扑功能"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: e753a435-38e0-482b-846b-121cb547555c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 3e7595baa26ba9eebfcb8a2fd5c2744d9c0fbfcb
ms.openlocfilehash: 42443f614b76b8180ac163b9889163021adbf048
ms.lasthandoff: 02/23/2017

---

# <a name="introduction-to-topology-in-azure-network-watcher"></a>Azure 网络观察程序中的拓扑简介

拓扑返回虚拟网络中的网络资源图。 此图描绘了资源之间的相互关系以表示端到端网络连接。

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

![拓扑概述][1]

在门户中，拓扑基于每个虚拟网络返回资源对象。 网络观察程序区域外部资源之间的关系用线条绘出（即使这些关系不会显示在资源组中）。 在门户视图中返回的资源是绘制的网络组件的子集。 若要查看网络资源的完整列表，可以使用 [PowerShell](network-watcher-topology-powershell.md) 或 [REST](network-watcher-topology-rest.md)

> [!NOTE]
> 要在其上运行拓扑的每个区域都需要网络观察程序实例。

返回资源时，将在两种关系下为资源之间的连接建模。

- **包含** - 示例：虚拟网络包含一个包含 NIC 的子网
- **已关联** - 示例：NIC 与 VM 相关联

### <a name="next-steps"></a>后续步骤

通过访问[使用 PowerShell 查看网络观察程序拓扑](network-watcher-topology-powershell.md)，了解如何使用 PowerShell 检索拓扑视图

<!--Image references-->

[1]: ./media/network-watcher-topology-overview/topology.png

