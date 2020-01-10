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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774635"
---
1. 单击 "**创建资源**" > "**全部查看**"。

    > [!div class="mx-imgBorder"]
    > ![搜索对等互连](../media/setup-seeall.png)

1. 在搜索框中搜索对*等互连*，并按键盘上的*Enter 键*。 在结果中，单击 "对**等互连**资源"。

    > [!div class="mx-imgBorder"]
    > ![启动对等互连](../media/setup-launch.png)

1. 启动对**等互连**后，请查看页面以了解详细信息。 准备就绪后，单击 "**创建**"。

    > [!div class="mx-imgBorder"]
    > ![创建对等互连](../media/setup-create.png)

1. 在 "**创建对等互连**" 页的 "**基本**信息" 选项卡下，按如下所示填写字段。

    > [!div class="mx-imgBorder"]
    > ![对等互连基础知识](../media/setup-basics-tab.png)

    * 选择 Azure**订阅**。
    * 对于 "**资源组**"，可从下拉选择现有的资源组，**或单击 "新建"** 创建一个新组。 我们将为此示例创建一个新的资源组。
    * **名称**对应于资源名称，可以是你选择的任何内容。
    * 如果选择上述步骤中的现有资源组，则会自动选择**区域**。 如果选择创建新的资源组，则还需要选择要在其中驻留资源的 Azure 区域。 美国东部

        > [!NOTE]
        > 资源组所在的区域独立于要与 Microsoft 建立对等互连的位置。 但最好是在驻留在最近的 Azure 区域内的资源组中组织对等互连资源。 例如：对于阿什本中的对等互连，可以在*美国东部*或*东部 2*创建资源组。

    * 在 "**对等 asn** " 字段中选择你的 ASN。

        > [!IMPORTANT]
        > * 提交对等请求之前，只能选择 ValidationState 为 "已批准" 的 ASN。 如果你刚提交了 PeerAsn 请求，请等待12小时，否则 ASN 关联将被 "批准"。 如果选择的 ASN 正在等待验证，你将看到一条错误消息。 
        > * 如果看不到需要选择的 ASN，请检查是否选择了正确的订阅。 如果已创建 PeerAsn，请检查是否已使用[将对等 ASN 连接到 Azure 订阅](../howto-subscription-association-portal.md)创建了。

        > [!div class="mx-imgBorder"]
        > ![对等互连基础知识](../media/setup-direct-basics-filled-tab.png)

    * 单击 "**下一步：配置 >** 继续。
