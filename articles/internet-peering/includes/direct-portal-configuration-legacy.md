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
ms.openlocfilehash: 16909e0a5d59b0ae019d91aad00e0168b0c5f433
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775064"
---
1. 在 "**创建对等互连**" 页的 "**配置**" 选项卡下，按如下所示填写字段。

    > [!div class="mx-imgBorder"]
    > ![对等互连配置-Direct](../media/setup-direct-conf-tab.png)

    * 对于 "对**等类型**"，请选择 "*直接*"。
    * 对于**Microsoft 网络**，请选择 " *AS8075*"。 请勿选择 "ASN 8069"。 它是为特殊应用程序保留的，仅供[Microsoft 对等互连](mailto:peering@microsoft.com)使用。
    * 选择 " **SKU**为*基本免费*"。 请勿选择 "*高级免费*"，因为它是为特殊应用程序保留的。
    * 选择要将对等互连转换为 Azure 资源的**地铁**位置。 如果在所选**地铁**位置中有 Microsoft 的对等互连连接未转换为 Azure 资源，则此类连接将在 "对**等互连连接**" 部分列出，如下所示。 你现在可以将这些对等互连连接转换为 Azure 资源。

        > [!div class="mx-imgBorder"]
        > ![对等互连配置-直接-旧连接](../media/setup-directlegacy-conf-tab.png)

1. 如果需要更新带宽，请单击下面突出显示的行的 "编辑" 按钮来修改连接设置。

    > [!div class="mx-imgBorder"]
    > ![对等互连配置-直接编辑](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > 如果要在所选**地铁**位置添加与 Microsoft 的其他对等互连连接，则**可以单击 "新建"** 按钮来执行此操作。 有关详细信息，请参阅[使用门户创建或修改直接对等互连](../howto-direct-portal.md)。
    >

1. 单击 "**查看" + "创建**"。 观察门户对输入的信息运行基本验证。 这会显示在顶部的功能区中，如*运行最终验证 ...* 。

    > [!div class="mx-imgBorder"]
    > ![对等验证 "选项卡](../media/setup-direct-review-tab-validation.png)

1. 完成*验证*后，请通过单击 "**创建**" 来验证你的信息并提交请求。 如果需要修改请求，请单击 "上**一步**"，然后重复上述步骤。

    > [!div class="mx-imgBorder"]
    > ![对等互连](../media/setup-direct-review-tab-submit.png)

1. 提交请求后，请等待它完成部署。 如果部署失败，请联系[Microsoft 对等互连](mailto:peering@microsoft.com)。 成功的部署将如下所示。

    > [!div class="mx-imgBorder"]
    > ![对等互连成功](../media/setup-direct-success.png)
