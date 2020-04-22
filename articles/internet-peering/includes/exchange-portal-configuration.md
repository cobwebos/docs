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
ms.openlocfilehash: 268703081a2a40e8bcc665889eaeaf8edd673bfd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680963"
---
1. 在"**创建对等"** 页上，"**配置"** 选项卡上填写如图所示的框。

    > [!div class="mx-imgBorder"]
    > ![创建对等页交换对等互连类型](../media/setup-exchange-conf-tab.png)

    * 对于**对等式**，选择 **"交换**"。
    * 选择**SKU**作为**基本免费**。
    * 选择要设置对等互连的**地铁**位置。

        > [!NOTE]
        > 如果您在所选**的 Metro**位置中已与 Microsoft 建立了对等互连连接，并且首次使用门户在该位置设置对等互连，则现有的对等互连连接将列在 **"对等互连连接**"部分中，如图所示。 Microsoft 将自动将这些对等连接转换为 Azure 资源，以便您可以在一个位置管理这些连接以及新连接。 有关详细信息，请参阅[通过使用门户将旧 Exchange 对等对等转换为 Azure 资源](../howto-legacy-exchange-portal.md)。
        >

1. 在 **"对等互连连接**"下，选择 **"新建**"以为要设置的每个新连接添加一条线。

    * 要配置或修改连接设置，请选择线路的编辑按钮。

        > [!div class="mx-imgBorder"]
        > ![编辑按钮](../media/setup-exchange-conf-tab-edit.png)

    * 要删除行，请选择 **...** > **删除**。

        > [!div class="mx-imgBorder"]
        > ![“删除”按钮](../media/setup-exchange-conf-tab-delete.png)

    * 您需要提供连接的所有设置，如下所示。

         > [!div class="mx-imgBorder"]
         > ![交换对等连接页](../media/setup-exchange-conf-tab-connection.png)

        1. 选择需要设置连接的**对等设施**。
        1. 在**IPv4 地址**和**IPv6 地址**框中，分别输入使用邻居命令在 Microsoft 路由器中配置的 IPv4 和 IPv6 地址。
        1. 输入您将在 **"最大通告的 IPv4 地址"** 和 **"最大通告的 IPv6 地址**"框中通告的 IPv4 和 IPv6 前缀的数量。
        1. 选择 **"确定"** 以保存连接设置。

1. 重复此步骤，在之前选择的**Metro**中，在 Microsoft 与您的网络同地连接的任何设施中添加更多连接。

1. 添加所有必需的连接后，选择 **"查看 + 创建**"。

    > [!div class="mx-imgBorder"]
    > ![对等配置选项卡](../media/setup-exchange-conf-tab-final.png)

1. 请注意，门户运行您输入的信息的基本验证。 顶部的功能区显示消息 *"正在运行最终验证..."。*

    > [!div class="mx-imgBorder"]
    > ![对等验证选项卡](../media/setup-direct-review-tab-validation.png)

1. 消息更改为*验证后*，验证您的信息。 通过选择 **"创建**"提交请求。 要修改请求，请选择 **"上一步"** 并重复这些步骤。

    > [!div class="mx-imgBorder"]
    > ![对等提交](../media/setup-exchange-review-tab-submit.png)

1. 提交请求后，等待部署完成。 如果部署失败，请与[微软对等互连](mailto:peering@microsoft.com)。 成功部署如下所示。

    > [!div class="mx-imgBorder"]
    > ![窥视成功](../media/setup-direct-success.png)
