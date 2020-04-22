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
ms.openlocfilehash: e50f53c65c330265d3d9ec1b1804a7910b632b1d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678851"
---
1. 转到**资源组**，然后选择创建**对等资源**时选择的资源组。 如果资源组太多，请使用 **"筛选器"** 框。

    > [!div class="mx-imgBorder"]
    > ![资源组](../media/setup-direct-get-resourcegroup.png)

1. 选择您创建的**对等资源**。

    > [!div class="mx-imgBorder"]
    > ![对等资源视图](../media/setup-direct-get-open.png)

1. **"概述"** 页显示高级信息，如下所示。

    > [!div class="mx-imgBorder"]
    > ![对等资源概述窗格](../media/setup-direct-get-overview.png)

1. 在左侧，选择**ASN 信息**以查看创建 PeerAsn 时提交的信息。

    > [!div class="mx-imgBorder"]
    > ![对等资源 ASN 信息](../media/setup-direct-get-asninfo.png)

1. 在左侧，选择**连接**。 在屏幕顶部，您会看到 ASN 和 Microsoft 之间跨地铁内不同设施的对等互连连接的摘要。 您还可以通过选择窗格中心的 **"连接**"来从 **"概述"** 页访问连接摘要，如图所示。

    > [!div class="mx-imgBorder"]
    > ![对等资源连接](../media/setup-direct-get-connectionssummary.png)

    * **连接状态**对应于对等互连连接设置的状态。 此字段中显示的状态遵循[Direct 对等演练](../walkthrough-direct-all.md)中显示的状态图。
    * **IPv4 会话状态**和**IPv6 会话状态**分别对应于 IPv4 和 IPv6 BGP 会话状态。 
    * 当您在屏幕顶部选择一行时，底部的 **"连接**"部分将显示每个连接的详细信息。 选择要展开**配置****、IPv4 地址**和**IPv6 地址**的箭头。
