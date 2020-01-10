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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774544"
---
成功部署对**等互连**资源后，可以按照以下步骤进行查看。

1. 请在创建对**等互连**资源时，单击 "**资源组**" 并单击所选的资源组。 如果资源组太多，则可以使用*筛选器*字段。

    > [!div class="mx-imgBorder"]
    > ![对等互连资源组](../media/setup-direct-get-resourcegroup.png)

1. 单击创建的对**等互连**资源。

    > [!div class="mx-imgBorder"]
    > ![对等互连资源视图](../media/setup-direct-get-open.png)

1. "**概述**" 页显示高级信息。 观察下面突出显示的信息。

    > [!div class="mx-imgBorder"]
    > ![对等互连资源视图](../media/setup-exchange-get-overview.png)

1. 在左侧，单击 " **ASN 信息**" 查看创建 PeerAsn 时提交的信息

    > [!div class="mx-imgBorder"]
    > ![对等互连资源视图](../media/setup-direct-get-asninfo.png)

1. 在左侧，单击 "**连接**"。 请查看你的 ASN 与 Microsoft 之间的对等互连连接的摘要，并跨地铁中的不同设施。 还可以通过单击前面突出显示的中心窗格中的 "**连接**"，在 "**概述**" 页上看到 "连接摘要"。

    > [!div class="mx-imgBorder"]
    > ![对等互连资源视图](../media/setup-exchange-get-connectionssummary.png)

    * **连接状态**对应于对等互连连接设置的状态。 此字段中显示的状态遵循[Exchange 对等演练](../walkthrough-exchange-all.md)中显示的状态图
    * **Ipv4 会话状态**和**ipv6 会话状态**分别对应于 ipv4 和 ipv6 BGP 会话状态。  
    * 选择顶部的行时，底部的 "***连接***" 部分会显示每个连接的详细信息。 可以单击箭头标记展开子节***配置***、 ***IPv4 地址***和***IPv6 地址***

    > [!div class="mx-imgBorder"]
    > ![对等互连资源视图](../media/setup-exchange-get-connectionsipv4.png)
