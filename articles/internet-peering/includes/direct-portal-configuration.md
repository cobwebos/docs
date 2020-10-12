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
ms.openlocfilehash: fda22346a44388248e37473bc7891b8a130569c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "81681072"
---
1. 在 " **创建对等互连** " 页上的 " **配置** " 选项卡上，填写框，如下所示。

    * 对于 "对 **等类型**"，请选择 " **直接**"。
    * 对于 **Microsoft 网络**，请选择 " **AS8075**"。 不要通过 ASN 8069 创建对等互连。 它是为特殊应用程序保留的，仅供 [Microsoft 对等互连](mailto:peering@microsoft.com)使用。
    * 选择 " **SKU** 为 **基本免费**"。 请勿选择 "免费"，因为它是为特殊应用程序保留的。
    * 选择要设置对等互连的 **地铁** 位置。

        > [!NOTE]
        > 如果在所选 **地铁** 位置与 Microsoft 建立了对等互连，并且在该位置第一次使用 Azure 门户设置对等互连，则会在 "对 **等互连连接** " 部分中列出现有的对等互连连接，如下所示。 Microsoft 将自动将这些对等互连连接转换为 Azure 资源，以便可以将它们与新连接一起在同一位置进行管理。 有关详细信息，请参阅 [使用门户将旧直接对等互连转换为 Azure 资源](../howto-legacy-direct-portal.md)。
        >

1. 在 "对 **等连接**" 下，选择 " **新建** " 以便为要设置的每个新连接添加一行。

    * 若要配置或修改连接设置，请选择线条的 "编辑" 按钮。

        > [!div class="mx-imgBorder"]
        > ![编辑按钮](../media/setup-direct-conf-tab-edit.png)
    
    * 若要删除行，请选择 **...**  > **删除**。

        > [!div class="mx-imgBorder"]
        > ![“删除”按钮](../media/setup-direct-conf-tab-delete.png)

    * 需要为连接提供所有设置，如下所示。

         > [!div class="mx-imgBorder"]
         > ![直接对等连接页](../media/setup-direct-conf-tab-connection.png)

        1. 选择需要设置连接的对 **等互连设施** 。
        1. **会话地址提供程序** 用于确定哪些用户提供了在您的网络和 Microsoft 之间设置 BGP 会话所需的子网。 如果可以提供子网，请选择 " **对等**"。 否则，请选择 " **microsoft** 和 [microsoft 对等互连](mailto:peering@microsoft.com) " 将与你联系。 选择此选项将需要更长的时间来处理对等请求。 在某些情况下，Microsoft 可能无法提供子网，这将导致请求拒绝。
        1. 如果选择了 " **会话地址提供程序** " 选项作为 **对等节点**，请分别在 " **会话 Ipv4 前缀** " 和 " **会话 IPv6 前缀** " 框中输入 IPv4 和 IPv6 地址以及前缀掩码。
        1. 在 " **播发的 ipv4 地址** " 和 " **最大播发 ipv6 地址** 数" 框中，输入要播发的 IPv4 和 IPv6 前缀的数目。
        1. 调整 " **总带宽** " 滑块，以反映连接的带宽。
        1. 选择 " **保存** " 以保存连接设置。

1. 重复上一步，以在 Microsoft 与你的网络（你之前选择的 **Metro** ）中的任何设备上添加更多连接。

1. 添加所有所需的连接后，请选择 " **查看 + 创建**"。

    > [!div class="mx-imgBorder"]
    > ![对等互连配置选项卡最终](../media/setup-direct-conf-tab-final.png)

1. 请注意，门户会对所输入的信息运行基本验证。 顶部的功能区显示 *运行最终验证*的消息 ...。

    > [!div class="mx-imgBorder"]
    > ![对等验证选项卡](../media/setup-direct-review-tab-validation.png)

1. 消息更改为 " *验证通过*" 后，请验证你的信息。 通过选择 " **创建**" 提交请求。 若要修改请求，请选择 " **上一步** "，然后重复上述步骤。

    > [!div class="mx-imgBorder"]
    > ![对等互连](../media/setup-direct-review-tab-submit.png)

1. 提交请求后，等待部署完成。 如果部署失败，请联系 [Microsoft 对等互连](mailto:peering@microsoft.com)。 此时会显示成功的部署，如下所示。

    > [!div class="mx-imgBorder"]
    > ![对等互连成功](../media/setup-direct-success.png)
