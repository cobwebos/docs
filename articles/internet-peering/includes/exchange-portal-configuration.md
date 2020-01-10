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
ms.openlocfilehash: cd51eca0ea4563e1b56f74677df0829669d9e177
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774492"
---
1. 在 "**创建对等互连**" 页的 "**配置**" 选项卡下，按如下所示填写字段。

    > [!div class="mx-imgBorder"]
    > ![对等互连配置-Exchange](../media/setup-exchange-conf-tab.png)

    * 对于 "对**等互连类型**"，选择 " *Exchange*"。
    * 选择 " **SKU**为*基本免费*"。
    * 选择要设置对等互连的**地铁**位置。

        > [!NOTE]
        > 如果在所选**地铁**位置与 Microsoft 建立了对等互连，并且首次在该位置设置对等互连，则会在 "对**等互连连接**" 部分中列出现有的对等互连连接，如下所示。 Microsoft 将自动将这些对等互连连接转换为 Azure 资源，以便可以将它们与新连接一起在同一位置进行管理。有关详细信息，请参阅[使用门户将旧版 Exchange 对等互连转换为 Azure 资源](../howto-legacy-exchange-portal.md)。
        >

1. 在 "对**等连接**" 下，单击 "**新建**" 以便为要设置的每个新连接添加一行。

    * 若要配置/修改连接设置，请单击行的 "编辑" 按钮。

        > [!div class="mx-imgBorder"]
        > ![对等互连配置-Exchange 编辑](../media/setup-exchange-conf-tab-edit.png)

    * 若要删除行，请单击 **"..." 按钮**> **delete**。

        > [!div class="mx-imgBorder"]
        > ![对等互连配置-Exchange 编辑](../media/setup-exchange-conf-tab-delete.png)

    * 需要为连接提供所有设置，如下所示。

         > [!div class="mx-imgBorder"]
         > ![对等互连配置-Exchange 连接](../media/setup-exchange-conf-tab-connection.png)

        1. 选择需要设置连接的对**等互连设施**。
        1. 在 " **ipv4 地址**" 和 " **ipv6 地址**" 字段中，分别输入 ipv4 和 ipv6 地址，该地址将在使用邻居命令的 Microsoft 路由器中进行配置。
        1. 输入每个字段中要播发的 IPv4 和 IPv6 前缀的数量，它们分别是**播发的 ipv4 地址**和**最大播发 IPv6 地址**。
        1. 单击 **"确定"** 保存连接设置。

1. 重复上述步骤，在 Microsoft 与你的网络的任何场所中添加更多连接，这种情况在之前选择的**地铁**范围内。

1. 添加所有所需的连接后，单击 "**审核 + 创建**"。

    > [!div class="mx-imgBorder"]
    > ![对等互连配置选项卡最终](../media/setup-exchange-conf-tab-final.png)

1. 观察门户对输入的信息运行基本验证。 这会显示在顶部的功能区中，如*运行最终验证 ...* 。

    > [!div class="mx-imgBorder"]
    > ![对等验证 "选项卡](../media/setup-direct-review-tab-validation.png)

1. 完成*验证*后，请通过单击 "**创建**" 来验证你的信息并提交请求。 如果需要修改请求，请单击 "上**一步**"，然后重复上述步骤。

    > [!div class="mx-imgBorder"]
    > ![对等互连](../media/setup-exchange-review-tab-submit.png)

1. 提交请求后，请等待它完成部署。 如果部署失败，请联系[Microsoft 对等互连](mailto:peering@microsoft.com)。 成功的部署将如下所示。

    > [!div class="mx-imgBorder"]
    > ![对等互连成功](../media/setup-direct-success.png)
