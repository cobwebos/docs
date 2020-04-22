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
ms.openlocfilehash: 632490498b8dd13414657edb9518cd543ac07af6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678819"
---
1. 在"**创建对等"** 页上的 **"配置"** 选项卡上，填写如下所示的框。

    > [!div class="mx-imgBorder"]
    > ![创建对等页配置选项卡](../media/setup-direct-conf-tab.png)

    * 对于**对等式**，选择 **"直接**"。
    * 对于**微软网络**，请选择**AS8075**。 不要选择 ASN 8069。 它保留给特殊应用程序，并且只由[微软对等互连](mailto:peering@microsoft.com)使用。
    * 选择**SKU**作为**基本免费**。 不要选择"免费高级"，因为它是为特殊应用程序保留的。
    * 选择要将对等互连转换为 Azure 资源的**Metro**位置。 如果在选定的**Metro**位置中具有未转换为 Azure 资源的对等连接，则这些连接将列在 **"对等连接**"部分中，如图所示。 现在，您可以将这些对等连接转换为 Azure 资源。

        > [!div class="mx-imgBorder"]
        > ![对等互连连接列表](../media/setup-directlegacy-conf-tab.png)

1. 如果需要更新带宽，请选择线路的编辑按钮以修改连接设置。

    > [!div class="mx-imgBorder"]
    > ![编辑按钮](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > 如果要在所选**的 Metro**位置添加与 Microsoft 的其他对等互连连接，请选择"**创建新**"。 有关详细信息，请参阅[使用门户 创建或修改直接对等互连](../howto-direct-portal.md)。
    >

1. 选择“查看 + 创建”  。 请注意，门户运行您输入的信息的基本验证。 顶部的功能区显示消息 *"正在运行最终验证..."。*

    > [!div class="mx-imgBorder"]
    > ![对等验证选项卡](../media/setup-direct-review-tab-validation.png)

1. 消息更改为*验证后*，验证您的信息。 通过选择 **"创建**"提交请求。 要修改请求，请选择 **"上一步"** 并重复这些步骤。

    > [!div class="mx-imgBorder"]
    > ![对等提交](../media/setup-direct-review-tab-submit.png)

1. 提交请求后，等待部署完成。 如果部署失败，请与[微软对等互连](mailto:peering@microsoft.com)。 成功部署如下所示。

    > [!div class="mx-imgBorder"]
    > ![窥视成功](../media/setup-direct-success.png)
