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
ms.openlocfilehash: 7d7b9f847cdcc4ab4b1ff065425eebe07fb4d888
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775207"
---
1. 在 "**创建对等互连**" 页的 "**配置**" 选项卡下，按如下所示填写字段。

    > [!div class="mx-imgBorder"]
    > ![对等互连配置-Exchange](../media/setup-exchange-conf-tab.png)

    * 对于 "对**等互连类型**"，选择 " *Exchange*"。
    * 选择 " **SKU**为*基本免费*"。
    * 选择要将对等互连转换为 Azure 资源的**地铁**位置。 如果在所选**地铁**位置中有 Microsoft 的对等互连连接未转换为 Azure 资源，则此类连接将在 "对**等互连连接**" 部分列出，如下所示。 你现在可以将这些对等互连连接转换为 Azure 资源。

        > [!div class="mx-imgBorder"]
        > ![对等互连配置-Exchange-旧连接](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > 不能修改旧的对等互连连接的设置。 如果要在所选**地铁**位置添加与 Microsoft 的其他对等互连连接，则**可以单击 "新建"** 按钮来执行此操作。 有关详细信息，请参阅[使用门户创建或修改 Exchange 对等互连](../howto-exchange-portal.md)。
        >

1. 单击 "**查看" + "创建**"。 观察门户对输入的信息运行基本验证。 这会显示在顶部的功能区中，如*运行最终验证 ...* 。

    > [!div class="mx-imgBorder"]
    > ![对等验证 "选项卡](../media/setup-direct-review-tab-validation.png)

1. 完成*验证*后，请通过单击 "**创建**" 来验证你的信息并提交请求。 如果需要修改请求，请单击 "上**一步**"，然后重复上述步骤。

    > [!div class="mx-imgBorder"]
    > ![对等互连](../media/setup-exchange-review-tab-submit.png)

1. 提交请求后，请等待它完成部署。 如果部署失败，请联系[Microsoft 对等互连](mailto:peering@microsoft.com)。 成功的部署将如下所示。

    > [!div class="mx-imgBorder"]
    > ![对等互连成功](../media/setup-direct-success.png)
