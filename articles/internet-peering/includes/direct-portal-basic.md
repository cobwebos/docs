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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678580"
---
1. 选择 "**创建资源** > **"。**

    > [!div class="mx-imgBorder"]
    > ![搜索对等](../media/setup-seeall.png)

1. 在搜索框中搜索对**等互连**，然后在键盘上选择**Enter** 。 从结果中选择对**等互连**资源。

    > [!div class="mx-imgBorder"]
    > ![启动对等互连](../media/setup-launch.png)

1. 对**等互连**开始后，查看页面以了解详细信息。 准备就绪后，选择 "**创建**"。

    > [!div class="mx-imgBorder"]
    > ![创建对等](../media/setup-create.png)

1. 在 "**创建对等互连**" 页上的 "**基本**信息" 选项卡上，填写框，如下所示。

    > [!div class="mx-imgBorder"]
    > ![对等基本选项卡](../media/setup-basics-tab.png)

    * 选择 Azure**订阅**。
    * 对于 "**资源组**"，可以从下拉列表中选择现有资源组，也可以通过选择 "新建" 创建新**组。** 在此示例中，我们将创建一个新的资源组。
    * **名称**对应于资源名称，可以是你选择的任何内容。
    * 如果选择现有的资源组，则**区域**为 autoselected。 如果选择创建新的资源组，则还需要选择要在其中驻留资源的 Azure 区域。

        > [!NOTE]
        > 资源组所在的区域独立于要与 Microsoft 建立对等互连的位置。 但在驻留在最近的 Azure 区域中的资源组内组织对等互连资源是最佳实践。 例如，对于阿什本中的对等互连，可以在美国东部或东部2创建资源组。

    * 在 "**对等 asn** " 框中选择你的 ASN。

        > [!IMPORTANT]
        > * 在提交对等请求之前，只能选择 ValidationState 为 "已批准" 的 ASN。 如果你刚提交了 PeerAsn 请求，请等待12小时，或等待 ASN 关联获得批准。 如果选择的 ASN 正在等待验证，你将看到一条错误消息。 
        > * 如果看不到需要选择的 ASN，请检查是否选择了正确的订阅。 如果是这样，请检查是否已使用[将对等 ASN 连接到 Azure 订阅](../howto-subscription-association-portal.md)创建了 PeerAsn。

        > [!div class="mx-imgBorder"]
        > ![已填充对等基础](../media/setup-direct-basics-filled-tab.png)

    * 选择**下一步：配置 >** 继续。
