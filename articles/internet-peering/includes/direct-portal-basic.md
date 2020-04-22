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
ms.openlocfilehash: 00c24212706555667ad4680c086ece24f15ca59a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678580"
---
1. 选择 **"创建资源** > **查看所有**"。

    > [!div class="mx-imgBorder"]
    > ![搜索对等互连](../media/setup-seeall.png)

1. 在搜索框中搜索**对等，** 然后选择键盘上的 **"输入**"。 从结果中选择**对等资源**。

    > [!div class="mx-imgBorder"]
    > ![启动对等互连](../media/setup-launch.png)

1. **对等互连**开始后，请查看页面以了解详细信息。 准备就绪后，选择 **"创建**"。

    > [!div class="mx-imgBorder"]
    > ![创建对等互连](../media/setup-create.png)

1. 在"**创建对等"** 页上，"**基本"** 选项卡上填写此处所示的框。

    > [!div class="mx-imgBorder"]
    > ![对等基础知识选项卡](../media/setup-basics-tab.png)

    * 选择 Azure**订阅**。
    * 对于**资源组**，可以从下拉列表中选择现有资源组，也可以通过选择 **"创建新**"来创建新组。 我们将为此示例创建新的资源组。
    * **名称**对应于资源名称，可以是您选择的任何名称。
    * 如果选择现有资源组，则自动选择**区域**。 如果选择创建新资源组，还需要选择要资源驻留的 Azure 区域。

        > [!NOTE]
        > 资源组所在的区域与要与 Microsoft 创建对等互连的位置无关。 但是，最佳做法是在驻留在最近的 Azure 区域的资源组中组织对等资源。 例如，对于 Ashburn 中的对等互连，可以在美国东部或 US2 东部创建资源组。

    * 在 **"对等 ASN"框中选择 ASN。**

        > [!IMPORTANT]
        > * 在提交对等请求之前，您只能选择具有"已批准状态"的 ASN。 如果您刚刚提交了 PeerAsn 请求，请等待 12 小时左右，等待 ASN 关联获得批准。 如果您选择的 ASN 正在等待验证，您将看到一条错误消息。 
        > * 如果您没有看到需要选择的 ASN，请检查是否选择了正确的订阅。 如果是这样，请检查是否已通过使用[对等方 ASN 创建](../howto-subscription-association-portal.md)对等 Asn 到 Azure 订阅。

        > [!div class="mx-imgBorder"]
        > ![填写的对等基础知识](../media/setup-direct-basics-filled-tab.png)

    * 选择 **"下一步 ：配置>** 以继续。
