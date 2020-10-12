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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "81678819"
---
1. 在 " **创建对等互连** " 页上的 " **配置** " 选项卡上，填写框，如下所示。

    > [!div class="mx-imgBorder"]
    > ![创建对等页配置选项卡](../media/setup-direct-conf-tab.png)

    * 对于 "对 **等类型**"，请选择 " **直接**"。
    * 对于 **Microsoft 网络**，请选择 " **AS8075**"。 请勿选择 ASN 8069。 它是为特殊应用程序保留的，仅供 [Microsoft 对等互连](mailto:peering@microsoft.com)使用。
    * 选择 " **SKU** 为 **基本免费**"。 请勿选择 "免费"，因为它是为特殊应用程序保留的。
    * 选择要将对等互连转换为 Azure 资源的 **地铁** 位置。 如果在所选 **地铁** 位置中有 Microsoft 的对等互连连接未转换为 Azure 资源，则这些连接将在 "对 **等互连连接** " 部分列出，如下所示。 你现在可以将这些对等互连连接转换为 Azure 资源。

        > [!div class="mx-imgBorder"]
        > ![对等连接列表](../media/setup-directlegacy-conf-tab.png)

1. 如果需要更新带宽，请选择线条的 "编辑" 按钮来修改连接设置。

    > [!div class="mx-imgBorder"]
    > ![编辑按钮](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > 如果要在所选 **地铁** 位置添加与 Microsoft 的其他对等互连连接，请选择 " **新建**"。 有关详细信息，请参阅 [使用门户创建或修改直接对等互连](../howto-direct-portal.md)。
    >

1. 选择“查看 + 创建”  。 请注意，门户会对所输入的信息运行基本验证。 顶部的功能区显示 *运行最终验证*的消息 ...。

    > [!div class="mx-imgBorder"]
    > ![对等验证选项卡](../media/setup-direct-review-tab-validation.png)

1. 消息更改为 " *验证通过*" 后，请验证你的信息。 通过选择 " **创建**" 提交请求。 若要修改请求，请选择 " **上一步** "，然后重复上述步骤。

    > [!div class="mx-imgBorder"]
    > ![对等互连](../media/setup-direct-review-tab-submit.png)

1. 提交请求后，等待部署完成。 如果部署失败，请联系 [Microsoft 对等互连](mailto:peering@microsoft.com)。 此时会显示成功的部署，如下所示。

    > [!div class="mx-imgBorder"]
    > ![对等互连成功](../media/setup-direct-success.png)
