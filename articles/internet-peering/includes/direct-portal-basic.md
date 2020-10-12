---
title: include 文件
titleSuffix: Azure
description: include 文件
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 5/22/2020
ms.author: derekol
ms.openlocfilehash: e2804c8f9b1af89ac0ea86ec14136df66d900060
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83846108"
---
1. 选择“创建资源” > “查看全部” 。

    > [!div class="mx-imgBorder"]
    > ![搜索对等互连](../media/setup-seeall.png)

1. 在搜索框中搜索“对等互连”，然后在键盘上选择“Enter” 。 在结果中，选择“对等互连”资源。

    > [!div class="mx-imgBorder"]
    > ![启动对等互连](../media/setup-launch.png)

1. 对等互连启动后，请查看页面以了解详细信息。 准备就绪后，选择“创建”。

    > [!div class="mx-imgBorder"]
    > ![创建对等互连](../media/setup-create.png)

1. 在“创建对等互连”页的“基本信息”选项卡上，按如下所示填写相应的框 。

    > [!div class="mx-imgBorder"]
    > ![对等互连基本信息选项卡](../media/setup-basics-tab.png)

    * 选择 Azure 订阅。
    * 对于“资源组”，可以从下拉列表中选择现有资源组，也可以通过选择“新建”创建新组 。 在此示例中，我们将创建新的资源组。
    * “名称”对应于资源名称，可以是所选择的任何内容。
    * 如果选择现有资源组，则会自动选择区域。 如果选择创建新的资源组，则还需要选择要在其中存放资源的 Azure 区域。

        > [!NOTE]
        > 资源组所在的区域独立于要与 Microsoft 创建对等互连的位置。 但是，最佳做法是在位于最近的 Azure 区域的资源组内组织对等互连资源。 例如，对于阿什本中的对等互连，可以在美国东部或美国东部 2 创建资源组。

    * 在“Peer ASN”框中选择你的 ASN。

        > [!IMPORTANT]
        > * 在提交对等互连请求之前，只能选择 ValidationState 为“已批准”的 ASN。 如果刚刚提交了 PeerAsn 请求，请等待 12 小时，或等待 ASN 关联获得批准。 如果选择的 ASN 正在等待验证，你将看到一条错误消息。 
        > * 如果看不到需要选择的 ASN，请检查是否选择了正确的订阅。 如果订阅正确，请检查是否已使用[将对等 ASN 关联到 Azure 订阅](../howto-subscription-association-portal.md)创建了 PeerAsn。

        > [!div class="mx-imgBorder"]
        > ![填写好的对等互连基本信息](../media/setup-direct-basics-filled-tab.png)

    * **选择“下一步:配置 >”** 以继续。
