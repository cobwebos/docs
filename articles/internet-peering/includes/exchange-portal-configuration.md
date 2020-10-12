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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "81680963"
---
1. 在 " **创建对等互连** " 页上的 " **配置** " 选项卡上，填写所示的框。

    > [!div class="mx-imgBorder"]
    > ![创建对等互连页面交换对等互连类型](../media/setup-exchange-conf-tab.png)

    * 对于 "对 **等互连类型**"，选择 " **Exchange**"。
    * 选择 " **SKU** 为 **基本免费**"。
    * 选择要设置对等互连的 **地铁** 位置。

        > [!NOTE]
        > 如果在所选 **地铁** 位置与 Microsoft 建立了对等互连，并且首次使用门户在该位置设置对等互连，则会在 "对 **等互连连接** " 部分中列出现有的对等互连连接，如下所示。 Microsoft 将自动将这些对等互连连接转换为 Azure 资源，以便你可以在一个位置管理所有这些连接和新连接。 有关详细信息，请参阅 [使用门户将旧版 Exchange 对等互连转换为 Azure 资源](../howto-legacy-exchange-portal.md)。
        >

1. 在 "对 **等连接**" 下，选择 " **新建** " 以便为要设置的每个新连接添加一行。

    * 若要配置或修改连接设置，请选择线条的 "编辑" 按钮。

        > [!div class="mx-imgBorder"]
        > ![编辑按钮](../media/setup-exchange-conf-tab-edit.png)

    * 若要删除行，请选择 **...**  > **删除**。

        > [!div class="mx-imgBorder"]
        > ![“删除”按钮](../media/setup-exchange-conf-tab-delete.png)

    * 需要为连接提供所有设置，如下所示。

         > [!div class="mx-imgBorder"]
         > ![Exchange 对等连接页](../media/setup-exchange-conf-tab-connection.png)

        1. 选择需要设置连接的对 **等互连设施** 。
        1. 在 " **ipv4 地址** " 和 " **ipv6 地址** " 框中，分别输入 ipv4 和 ipv6 地址，这些地址将通过使用 "邻居" 命令在 Microsoft 路由器中进行配置。
        1. 在 " **播发的 ipv4 地址** " 和 " **最大播发 ipv6 地址** 数" 框中，输入要播发的 IPv4 和 IPv6 前缀的数目。
        1. 选择 **"确定"** 保存连接设置。

1. 重复此步骤，以便在 Microsoft 与你的 **网络的任何** 场所添加更多连接。

1. 添加所有所需的连接后，请选择 " **查看 + 创建**"。

    > [!div class="mx-imgBorder"]
    > ![对等互连配置选项卡](../media/setup-exchange-conf-tab-final.png)

1. 请注意，门户会对所输入的信息运行基本验证。 顶部的功能区显示 *运行最终验证*的消息 ...。

    > [!div class="mx-imgBorder"]
    > ![对等验证选项卡](../media/setup-direct-review-tab-validation.png)

1. 消息更改为 " *验证通过*" 后，请验证你的信息。 通过选择 " **创建**" 提交请求。 若要修改请求，请选择 " **上一步** "，然后重复上述步骤。

    > [!div class="mx-imgBorder"]
    > ![对等互连](../media/setup-exchange-review-tab-submit.png)

1. 提交请求后，等待部署完成。 如果部署失败，请联系 [Microsoft 对等互连](mailto:peering@microsoft.com)。 此时会显示成功的部署，如下所示。

    > [!div class="mx-imgBorder"]
    > ![对等互连成功](../media/setup-direct-success.png)
