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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775207"
---
1. 在"**创建对等"** 页上，**在"配置"** 选项卡下填写字段，如下所示。

    > [!div class="mx-imgBorder"]
    > ![对等配置 - 交换](../media/setup-exchange-conf-tab.png)

    * 对于**对等式**，选择 *"交换*"。
    * 选择**SKU**作为*基本免费*。
    * 选择要将对等互连转换为 Azure 资源的 **"地铁"** 位置。 如果在选定的**Metro**位置中具有未转换为 Azure 资源的对等互连连接，则此类连接将显示在 **"对等互连连接**"部分中，如下所示。 现在，您可以将这些对等互连连接转换为 Azure 资源。

        > [!div class="mx-imgBorder"]
        > ![对等配置 - 交换 - 旧连接](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > 不能修改旧对等互连连接的设置。 如果要在所选**的 Metro**位置添加与 Microsoft 的其他对等互连连接，可以通过单击"**创建新**按钮"来执行此操作。 有关详细信息[，请参阅使用门户创建或修改 Exchange 对等互连](../howto-exchange-portal.md)。
        >

1. 单击 **"审阅 + 创建**"。 请注意，门户运行您输入的信息的基本验证。 这显示在顶部的功能区中，如*正在运行最终验证...*

    > [!div class="mx-imgBorder"]
    > ![对等验证选项卡](../media/setup-direct-review-tab-validation.png)

1. 在它变为*验证通过*后，验证您的信息，并通过单击"**创建**"提交请求。 如果需要修改请求，请单击 **"上一步"** 并重复上述步骤。

    > [!div class="mx-imgBorder"]
    > ![对等提交](../media/setup-exchange-review-tab-submit.png)

1. 提交请求后，请等待它完成部署。 如果部署失败，请与[微软对等互连](mailto:peering@microsoft.com)。 成功部署将显示如下。

    > [!div class="mx-imgBorder"]
    > ![窥视成功](../media/setup-direct-success.png)
