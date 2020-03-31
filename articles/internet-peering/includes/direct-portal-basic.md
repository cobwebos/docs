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
ms.openlocfilehash: 79cf4b2edd1a25df427cf15f9ee7f2f331ebd2df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774635"
---
1. 单击 **"创建资源** > **查看所有**"。

    > [!div class="mx-imgBorder"]
    > ![搜索对等互连](../media/setup-seeall.png)

1. 在搜索框中搜索*对等，* 然后按键盘上的 *"输入*"。 从结果中，单击**对等资源**。

    > [!div class="mx-imgBorder"]
    > ![启动对等互连](../media/setup-launch.png)

1. 启动**对等互连**后，请查看页面以了解详细信息。 准备就绪后，单击"**创建**"。

    > [!div class="mx-imgBorder"]
    > ![创建对等互连](../media/setup-create.png)

1. 在"**创建对等"** 页上，在 **"基本"** 选项卡下填写字段，如下所示。

    > [!div class="mx-imgBorder"]
    > ![对等基础知识](../media/setup-basics-tab.png)

    * 选择 Azure**订阅**。
    * 对于**资源组**，可以从下拉列表中选择现有资源组，也可以单击"**创建新**"来创建新组。 我们将为此示例创建新的资源组。
    * **名称**对应于资源名称，可以是您选择的任何名称。
    * 如果在上述步骤中选择现有资源组，则**自动选择区域**。 如果选择创建新资源组，则还需要选择希望资源驻留的 Azure 区域。 美国东部

        > [!NOTE]
        > 资源组所在的区域与要与 Microsoft 创建对等互连的位置无关。 但最佳做法是，在驻留在最近的 Azure 区域的资源组中组织对等资源。 例如：对于 Ashburn 中的对等互连，您可以在*美国东部*或*US2 中*创建资源组

    * 在 **"对等 ASN"字段中选择 ASN。**

        > [!IMPORTANT]
        > * 在提交对等请求之前，您只能选择具有验证状态的 ASN 作为"已批准"。 如果您刚刚提交了您的 PeerAsn 请求，请等待 12 小时左右，以便 ASN 关联"已批准"。 如果您选择的 ASN 正在等待验证，您将看到一条错误消息。 
        > * 如果看不到需要选择的 ASN，请检查您是否选择了正确的订阅。 如果是这样，请检查是否已使用[对等方 ASN 创建了](../howto-subscription-association-portal.md)对等 Asn 到 Azure 订阅 。

        > [!div class="mx-imgBorder"]
        > ![已填充对等基础知识](../media/setup-direct-basics-filled-tab.png)

    * 单击 **"下一步：配置>** 继续。
