---
title: include 文件
titleSuffix: Azure
description: include 文件
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: b967c844ab145074490e931122cbe092d67de0c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774544"
---
成功部署**对等资源**后，您可以按照以下步骤查看它。

1. 转到**资源组**，然后单击创建**对等资源**时选择的资源组。 如果资源组太多，则可以使用 *"筛选器"* 字段。

    > [!div class="mx-imgBorder"]
    > ![对等资源组](../media/setup-direct-get-resourcegroup.png)

1. 单击您创建的**对等资源**。

    > [!div class="mx-imgBorder"]
    > ![对等资源视图](../media/setup-direct-get-open.png)

1. **"概述"** 页显示高级信息。 请遵守下面突出显示的信息。

    > [!div class="mx-imgBorder"]
    > ![对等资源视图](../media/setup-exchange-get-overview.png)

1. 在左侧，单击**ASN 信息**以查看创建 PeerAsn 时提交的信息

    > [!div class="mx-imgBorder"]
    > ![对等资源视图](../media/setup-direct-get-asninfo.png)

1. 在左侧，单击**连接**。 在上面查看 ASN 和 Microsoft 之间跨地铁内不同设施的对等互连连接的摘要。 您还可以通过单击中心窗格中的**连接**（如上所示）从 **"概述"** 页面到达连接摘要。

    > [!div class="mx-imgBorder"]
    > ![对等资源视图](../media/setup-exchange-get-connectionssummary.png)

    * **连接状态**对应于建立的对等互连连接的状态。 此字段中显示的状态遵循[Exchange 对等式演练](../walkthrough-exchange-all.md)中显示的状态图
    * **IPv4 会话状态**和**IPv6 会话状态**分别对应于 IPv4 和 IPv6 BGP 会话状态。  
    * 选择顶部的行时，底部的 ***"连接***"部分将显示每个连接的详细信息。 您可以单击箭头标记来展开子节***配置******、IPv4 地址***和***IPv6 地址***

    > [!div class="mx-imgBorder"]
    > ![对等资源视图](../media/setup-exchange-get-connectionsipv4.png)
